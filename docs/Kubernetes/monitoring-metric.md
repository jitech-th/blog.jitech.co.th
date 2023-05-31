# Metric monitoring setup in K8S

## Stack

- `telegraf` - metric collector
- `influxdb` - time series database
- `grafana`  - monitoring dashboard

## Instructions

### Inlfuxdb

we will setup a time series database first

- #### Setup Influxdb using Helm Chart
> for more information about Helm [here](../Helm/basic)
```
$ helm pull --untar influxdata/influxdb
```

- #### Add a custom variable file
```
$ vim values-custom.yaml

image:
  repository: "arm64v8/influxdb" # <-- edit
  tag: "1.8.10"
  pullPolicy: IfNotPresent
...

persistence:
  enabled: true
  ## A manually managed Persistent Volume and Claim
  ## Requires persistence.enabled: true
  ## If defined, PVC must be created manually before volume will be bound
  # existingClaim:
  ## influxdb data Persistent Volume Storage Class
  ## If defined, storageClassName: <storageClass>
  ## If set to "-", storageClassName: "", which disables dynamic provisioning
  ## If undefined (the default) or set to null, no storageClassName spec is
  ##   set, choosing the default provisioner.  (gp2 on AWS, standard on
  ##   GKE, AWS & OpenStack)
  ##
  storageClass: "nfs-client"  # <-- edit this to use our custom storageClass
  annotations:
  accessMode: ReadWriteOnce
  size: 40Gi  # <-- edit size of the pvc
...

setDefaultUser:
  enabled: true
  user:
    ## The user name
    ## Default: "admin"
    username: "admin"

    ## User password
    ## single quotes must be escaped (\')
    ## Default: (Randomly generated 10 characters of AlphaNum)
    # password:

    ## The user name and password are obtained from an existing secret. The expected
    ## keys are `influxdb-user` and `influxdb-password`.
    ## If set, the username and password values above are ignored.
    existingSecret: influxdb-auth  # <-- use password from secret
```

- #### Create and deploy secret
```
# influx-auth.yaml
apiVersion: v1
kind: Secret
metadata:
  name: influxdb-auth
  namespace: monitoring
type: Opaque
data:
  influxdb-user: <base64_user [echo -n "user" | base64]>
  influxdb-password: "<base64_password [echo -n "password" | base64]>"

$ kubectl apply -f influx-auth.yaml
``` 

- #### Install influxdb from Helm
```
$ ls
influxdb
$ helm install monitoring-release -f ./influxdb/values-custom.yaml ./influxdb --namespace monitoring
$ helm list -n monitoring
NAME              	NAMESPACE 	REVISION	UPDATED                               	STATUS  	CHART          	APP VERSION
monitoring-release	monitoring	1       	2023-03-18 20:19:16.58953019 +0700 +07	deployed	influxdb-4.12.1	1.8.10 
```

### Telegraf
Now, we have a database. Next we will setup `telegraf` for a collector

- #### Setup telegraf using Kustomize
> for more information about Kustomize [here](../Kustomize/basic.md)
```
# telegraf-deploy.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: telegraf
  namespace: monitoring
spec:
  selector:
    matchLabels:
      name: telegraf
  template:
    metadata:
      labels:
        name: telegraf
    spec:
      containers:
      - name: telegraf
        image: docker.io/telegraf:latest
        env:
        - name: HOSTNAME
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName
        - name: "HOST_PROC"
          value: "/rootfs/proc"
        - name: "HOST_SYS"
          value: "/rootfs/sys"
        volumeMounts:
        - name: sys
          mountPath: /rootfs/sys
          readOnly: true
        - name: proc
          mountPath: /rootfs/proc
          readOnly: true
        - name: utmp
          mountPath: /var/run/utmp
          readOnly: true
        - name: thermal
          mountPath: /tmp/temp
          readOnly: true
        - name: nfs
          mountPath: /mnt/shared
          readOnly: true
        - name: config
          mountPath: /etc/telegraf
      terminationGracePeriodSeconds: 30
      volumes:
      - name: sys
        hostPath:
          path: /sys
      - name: proc
        hostPath:
          path: /proc
      - name: utmp
        hostPath:
          path: /var/run/utmp
      - name: thermal
        hostPath:
          path: /sys/class/thermal/thermal_zone0/temp
      - name: nfs
        hostPath:
          path: /mnt/shared/monitoring-monitoring-release-influxdb-data-monitoring-release-influxdb-0-pvc-d76718ec-c738-48b3-a9dd-ea8ee9d9ee29
      - name: config
        configMap:
          name: telegraf-config
```

```
# telegraf.conf

[global_tags]

# Configuration for telegraf agent
[agent]
  interval = "30s"
  round_interval = true
  metric_batch_size = 1000
  metric_buffer_limit = 10000
  collection_jitter = "0s"
  flush_interval = "30s"
  flush_jitter = "0s"
  precision = "0s"

  hostname = "${HOSTNAME}"
  omit_hostname = false

[[outputs.influxdb]]
  urls = ["http://monitoring-release-influxdb.monitoring:8086"]  #<-- from Helm output (clusterIP service)

  database = "telegraf"

  ## HTTP Basic Auth
  username = "<influxdb-username>"
  password = "<influxdb-password>"

[[inputs.cpu]]
  percpu = true
  totalcpu = true
  collect_cpu_time = false
  report_active = false

[[inputs.net]]
  interfaces = ["eth0"]

[[inputs.mem]]

[[inputs.disk]]
  mount_points = ["/", "/mnt/shared"]

[[inputs.system]]

[[inputs.file]]
  files = ["/tmp/temp"]
  data_format = "value"
  data_type = "integer"
```

```
# kustomization.yaml

amespace: monitoring
resources:
  - telegraf-deploy.yaml
configMapGenerator:
  - name: telegraf-config
    files:
    - telegraf.conf
```

- #### Deploy telegraf
```
$ kubectl apply -k .
```

### Grafana
Last, we need something to show metrics `telegraf` collected, that is `grafana`

- #### Setup grafana using Kustomize
```
# grafana-deploy.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: monitoring
  name: grafana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
      - image: docker.io/grafana/grafana:latest
        name: grafana
        volumeMounts:
          - name: data-dir
            mountPath: /var/lib/grafana/
      securityContext:
        fsGroup: 472
      volumes:
       - name: data-dir
         persistentVolumeClaim:
           claimName: grafana-pvc
```

```
# grafana-pvc.yaml

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  namespace: monitoring
  name: grafana-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: nfs-client
```

```
# grafana-svc.yaml
apiVersion: v1
kind: Service
metadata:
  namespace: monitoring
  name: grafana-service
spec:
  selector:
    app: grafana
  ports:
  - name: dashboard
    port: 3000
    targetPort: 3000
    nodePort: 30300
  type: NodePort
```

```
# kustomization.yaml
namespace: monitoring
resources:
  - grafana-deploy.yaml
  - grafana-svc.yaml
  - grafana-pvc.yaml
```

- #### Deploy `grafana`
```
$ kubectl apply -k .
```

- #### Validate

Navigate to IP of the cluster and port 30300 (nodePort) `http://<IP>:30300`