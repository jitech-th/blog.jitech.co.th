# Setting up a Gitlab runner

## Prerequisites
- Kubernetes
- Helm
- Gitlab

### Instructions

- #### add gitlab runner repo
```
$ helm repo add gitlab https://charts.gitlab.io
```

- #### edit `values.yaml` and insert the token
```
## GitLab Runner Image
gitlabUrl: https://gitlab.com

rbac:
  create: true
  rules:
    - apiGroups: [""]
      resources: ["pods"]
      verbs: ["list", "get", "watch", "create", "delete"]
    - apiGroups: [""]
      resources: ["pods/exec"]
      verbs: ["create"]
    - apiGroups: [""]
      resources: ["pods/log"]
      verbs: ["get"]
    - apiGroups: [""]
      resources: ["pods/attach"]
      verbs: ["list", "get", "create", "delete", "update"]
    - apiGroups: [""]
      resources: ["secrets"]
      verbs: ["list", "get", "create", "delete", "update"]      
    - apiGroups: [""]
      resources: ["configmaps"]
      verbs: ["list", "get", "create", "delete", "update"]      

runners:
  config: |
    [[runners]]
      [runners.kubernetes]
        namespace = "{{.Release.Namespace}}"
        image = "ubuntu:16.04"
        [runners.kubernetes.node_selector]
          "kubernetes.io/arch" = "arm64"
  privileged: true
  secret: gitlab-runner
```

- #### get token and convert to base64
```
$ echo "<raw token>" | base64
```

- #### create secret on Kubernetes
```
#gitlab-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: gitlab-runner
  namespace: gitlab
type: Opaque
data:
  runner-registration-token: "<base64 encoded token>"
  runner-token: ""
```
```
$ kubectl apply -f gitlab-secret.yaml -n gitlab
```

- #### install gitlab via helm
```
$ helm install --namespace gitlab gitlab-runner -f value.yaml gitlab/gitlab-runner
```

## Note
- การใช้ runner บน k8s ถ้าใช้ `helm` จะง่ายขึ้นมาก
- `helm` จะไป install component ที่จำเป็นต่างๆ ให้
- เมื่อ gitlab มี job ที่ถูกเข้าคิวไว้ runner จะไปดึงมาทำ
- โดย runner จะสร้าง container helper ขึ้นมาเพื่อทำ job ตาม pipeline
- ดังนั้นถ้า runner อยู่บน private network เท่ากับว่าสามารถคุยกับในวง private ได้ ต่อให้เป็น job ที่อยู่บน `gitlab.com`