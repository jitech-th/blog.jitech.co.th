---
tags:
    - Error
    - Kubernetes
    - DNS
---
# Can't resolve hostname in Kubernetes pods

- #### First, try to restart k3s
```
# on master node
$ systemctl restart k3s

# on worker node
$ systemctl restart k3s-agent
```

- #### Second, try to restart coredns
```
$ kubectl -n kube-system rollout restart deployment coredns
```

- #### Thrid, try to change upstream dns resolver

You need to change on all nodes
```
change in "nmtui"
then
$ systemctl restart NetworkManager
```

- #### Verify
```
$ kubectl get svc -n kube-system
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP                              PORT(S)                      AGE
kube-dns         ClusterIP      10.43.0.10      <none>                                   53/UDP,53/TCP,9153/TCP       46d
metrics-server   ClusterIP      10.43.134.231   <none>                                   443/TCP                      46d
traefik          LoadBalancer   10.43.96.99     192.168.2.11,192.168.2.12,192.168.2.13   80:30844/TCP,443:32368/TCP   46d

$ dig <hostname> @10.43.0.10
```