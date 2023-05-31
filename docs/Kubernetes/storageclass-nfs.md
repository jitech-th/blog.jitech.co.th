#NFS StorageClass

- show default StorageClass (local-path)

```
$ kubectl get storageclasses

NAME                   PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)   rancher.io/local-path   Delete          WaitForFirstConsumer   false                  3h46m
```

##Prerequisites

- Helm Chart

- Kubernetes

- NFS shared storage

##Instructions

####add nfs provisioner (which is dynamic provisioning)

```
$ helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
``` 

####install nfs provisioner

```
$ helm install nfs-sc nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
    --set nfs.server=x.x.x.x \
    --set nfs.path=/exported/path
```

####verify 

```
$ kubectl get sc
NAME                   PROVISIONER                                                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)   rancher.io/local-path                                           Delete          WaitForFirstConsumer   false                  10d
nfs-client             cluster.local/nfs-provisioner-nfs-subdir-external-provisioner   Delete          Immediate              true                   6d3h
```

####create PVC

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-blogs-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
  storageClassName: nfs-client
```

####verify PVC

```
$ kubectl get pvc
NAME              STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
nginx-blogs-pvc   Bound    pvc-456fb39d-caa5-42c4-85b2-888ecdef6946   5Gi        RWX            nfs-client     6d3h
```

####create instace

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-blogs
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-blogs
  template:
    metadata:
      labels:
        app: nginx-blogs
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - name: http				# out of
          containerPort: 80			# scope
        volumeMounts:
        - name: nginx-blogs-vol
          mountPath: /usr/share/nginx/html
        - name: nginx-blogs-config		# out of 
          mountPath: /etc/nginx/nginx.conf	# scope
          subPath: nginx.conf			# of this topic
      securityContext:
        fsGroup: 101
      volumes:
      - name: nginx-blogs-vol
        persistentVolumeClaim:
          claimName: nginx-blogs-pvc
      - name: nginx-blogs-config		# out of
        configMap:				# scope 
          name: nginx-blogs-config		# of this topic
```

[reference](https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner/blob/master/charts/nfs-subdir-external-provisioner/README.md)
