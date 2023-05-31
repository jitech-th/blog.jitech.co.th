# Move a specific pod to another node in K8S

## Instructions

- #### Cordon the node
```
$ kubectl cordon <node>
```

- #### delete the pod, k8s will recreate the Pod and assign it in a different node
```
$ kubectl delete pod <pod-name>
```

- #### uncordon the node
```
$ kubectl uncordon <node>
```