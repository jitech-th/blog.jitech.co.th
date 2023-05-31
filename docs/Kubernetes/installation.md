#K3S installation

K3S lightweight kubernestes

##Instructions

###Manager

- install script

```
$ curl -sfL https://get.k3s.io | K3S_NODE_NAME=<node_name> sh -
```

###Worker

- show token (on manager)

```
$ cat /var/lib/rancher/k3s/server/node-token
```

- install script

```
$ curl -sfL https://get.k3s.io | K3S_URL=https://<manager_ip>:6443 K3S_TOKEN=<token> K3S_NODE_NAME=<node_name> sh -
```

- verify

```
$ kubectl get node
NAME      STATUS   ROLES                  AGE   VERSION
server2   Ready    <none>                 10d   v1.25.6+k3s1
server1   Ready    control-plane,master   10d   v1.25.6+k3s1
server3   Ready    <none>                 10d   v1.25.6+k3s1
```

more options: [here](https://docs.k3s.io/reference/server-config)
