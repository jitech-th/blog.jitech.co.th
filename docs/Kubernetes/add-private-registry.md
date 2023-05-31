# Using Gitlab private registry in K8S

[How to push images to a private registry](../../Git/setup-registry/)

## Prerequisites

- gitlab account
- kubernetes cluster

## Instructions

- #### generate and get an access token with read_registry policy in Gitlab
```
<project_name> -> Settings -> Access Tokens 
```

- #### generate base64 credentials
```
$ echo -n "<REGISTRY_USERNAME>:<REGISTRY_TOKEN>" | base64
```

- #### create a Docker credentials file

```
#.dockerconfigjson
{
    "auths": {
        "https://registry.gitlab.com":{
            "username":"<REGISTRY_USERNAME>",
            "password":"<REGISTRY_TOKEN>",
            "email":"<REGISTRY_EMAIL>",
            "auth":"<base64_credentials_from_above>"
    	}
    }
}
```
- #### encode it to base64 for the k8s secret
```
$ cat .dockerconfigjson | base64
```

- #### create a K8S secret and apply
```
#registry-credentials.yaml
apiVersion: v1
kind: Secret
metadata:
  name: registry-credentials
  namespace: default
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: <BASE_64_ENCODED_DOCKERCONFIG>
```

```
$ kubectl apply -f registry-credentials.yml
```

- #### add ImagePullSecrets for registry authentication
```
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  template:
    metadata:
      ...
    spec:
      containers:
      - name: ...
      imagePullSecrets: 
      - name: registry-credentials
```

for more [Using Gitlab Private Registry with Kubernetes](https://chris-vermeulen.com/using-gitlab-registry-with-kubernetes/)