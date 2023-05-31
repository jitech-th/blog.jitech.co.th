---
tags:
    - Error
    - Kubernetes
---
# Kubernetes unreachable

## Logs
```
Error: Kubernetes cluster unreachable: Get "http://localhost:8080/version?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
```

## Solving
```
$ export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```