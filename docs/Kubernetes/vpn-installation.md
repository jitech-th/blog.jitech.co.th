# OpenVPN installation on K8S

## Prerequisites
- certificates and keys
- K8S cluster

## Instructions
- #### verify we generated keys and certificates
```
$ ls -l keys/
total 36
-rw------- 1 root root 1200 Feb 26 12:37 ca.crt
-rw------- 1 root root 4506 Feb 26 13:34 client.crt
-rw------- 1 root root 1704 Feb 26 12:38 client.key
-rw------- 1 root root  424 Feb 26 12:37 dh.pem
-rw------- 1 root root 4642 Feb 26 12:38 server.crt
-rw------- 1 root root 1704 Feb 26 12:38 server.key
-rw------- 1 root root  636 Feb 26 13:12 ta.key
```

- #### create openvpn namespace
```
$ kubectl create namespace openvpn
```

- #### create secret for keys
```
$ kubectl -n openvpn create secret generic ovpn-cert --from-file=keys/
```

- #### create `ovpn_env` file to be used in Docker image
```
# openvpn-config.yaml
apiVersion: v1
data:
  openvpn.conf: |
    port 1194
    proto tcp
    dev tun

    ca /etc/openvpn/keys/ca.crt
    cert /etc/openvpn/keys/server.crt
    key /etc/openvpn/keys/server.key
    dh /etc/openvpn/keys/dh.pem
    tls-auth /etc/openvpn/keys/ta.key 0
    tls-server
    auth SHA512
    cipher AES-256-CBC
    tls-version-min 1.2
    tls-cipher TLS-ECDHE-ECDSA-WITH-AES-256-GCM-SHA384
    duplicate-cn

    server 192.168.100.0 255.255.255.192
    topology subnet

    push "dhcp-option DNS 1.1.1.1"
    push "dhcp-option DNS 8.8.8.8"
    push "route 192.168.2.0 255.255.255.0"
    push "redirect-gateway"
    keepalive 10 60

    persist-key
    persist-tun
    user nobody
    group nogroup


    status /tmp/openvpn-status.log
    status-version 1
    verb 3

    script-security 1

  ovpn_env.sh: |
    declare -x OVPN_AUTH=SHA512
    declare -x OVPN_CIPHER=AES-256-CBC
    declare -x OVPN_CLIENT_TO_CLIENT=
    declare -x OVPN_CN=<server_name>
    declare -x OVPN_COMP_LZO=0
    declare -x OVPN_DEFROUTE=1
    declare -x OVPN_DEVICE=tun
    declare -x OVPN_DEVICEN=0
    declare -x OVPN_DISABLE_PUSH_BLOCK_DNS=1
    declare -x OVPN_DNS=1
    declare -x OVPN_DNS_SERVERS=([0]="1.1.1.1" [1]="8.8.8.8")
    declare -x OVPN_ENV=/etc/openvpn/ovpn_env.sh
    declare -x OVPN_EXTRA_CLIENT_CONFIG=()
    declare -x OVPN_EXTRA_SERVER_CONFIG=([0]="topology subnet")
    declare -x OVPN_FRAGMENT=
    declare -x OVPN_KEEPALIVE='10 60'
    declare -x OVPN_MTU=
    declare -x OVPN_NAT=0
    declare -x OVPN_PORT=31194
    declare -x OVPN_PROTO=tcp
    declare -x OVPN_PUSH=()
    declare -x OVPN_ROUTES=()
    declare -x OVPN_SERVER=192.168.100.0/26
    declare -x OVPN_SERVER_URL=tcp://<server_name>:31194
    declare -x OVPN_TLS_CIPHER=TLS-ECDHE-ECDSA-WITH-AES-256-GCM-SHA384
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: ovpn-conf
  namespace: openvpn
```
**note:** the env information must match to the openvpn configuration

**note:** if you already have configuration files you can inject them to yaml like this:
```
$ kubectl create configmap --dry-run=client somename --from-file=./conf/server.conf --output yaml | tee somename.yaml
```

- #### create openvpn deployment
```
# openvpn.yaml
apiVersion: v1
kind: Service
metadata:
  name: openvpn-svc
  namespace: openvpn
  labels:
    app: openvpn
spec:
  selector:
    app: openvpn
  ports:
  - name: openvpn
    port: 1194
    protocol: TCP
    targetPort: 1194
    nodePort: 31194
  type: NodePort
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: openvpn
  namespace: openvpn
  labels:
    app: openvpn
spec:
  replicas: 1
  selector:
    matchLabels:
      app: openvpn
  template:
    metadata:
      name: openvpn
      labels:
        app: openvpn
    spec:
      # imagePullSecrets:               ## for custom image
      # - name: registry-credentials    ## in a private registry
      containers:
      - image: kylemanna/openvpn
        imagePullPolicy: IfNotPresent
        name: openvpn
        ports:
        - containerPort: 1194
          name: openvpn
          protocol: TCP
        readinessProbe:
          tcpSocket:
            port: 1194
          initialDelaySeconds: 5
          periodSeconds: 30
          timeoutSeconds: 5
          successThreshold: 1
          failureThreshold: 3
        # In addition to the readiness probe, this configuration includes a liveness probe.
        # The kubelet will run the first liveness probe 15 seconds after the container starts.
        # Similar to the readiness probe, this will attempt to connect to the container on port 1194.
        # If the liveness probe fails, the container will be restarted.
        livenessProbe:
          tcpSocket:
            port: 1194
          initialDelaySeconds: 20
          periodSeconds: 90
          timeoutSeconds: 10
          successThreshold: 1
          failureThreshold: 2
          #resources:
          #  limits:
          #    cpu: 1000m
          #    memory: 128Mi
          #  requests:
          #    cpu: 10m
          #    memory: 16Mi
        securityContext:
          capabilities:
            add:
            - NET_ADMIN
        volumeMounts:
        - mountPath: /etc/openvpn/keys
          name: ovpn-cert
        - mountPath: /etc/openvpn
          name: ovpn-conf
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      volumes:
      - name: ovpn-cert
        secret:
          defaultMode: 0420
          secretName: ovpn-cert
      - configMap:
          defaultMode: 0644
          name: ovpn-conf
        name: ovpn-conf
```

- #### run openvpn
```
$ kubectl apply -f openvpn-config.yaml
$ kubectl apply -f openvpn.yaml
```