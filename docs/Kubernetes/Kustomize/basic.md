# Kustomize for beginners

## Method 1

- #### Show directory
```
$ tree
.
├── db
│   ├── db-config.yaml
│   ├── db-depl.yaml
│   └── db-service.yaml
├── kustomization.yaml
├── message-broker
│   ├── rabbitmq-config.yaml
│   ├── rabbitmq-depl.yaml
│   └── rabbitmq-service.yaml
└── nginx
    ├── nginx-depl.yaml
    └── nginx-service.yaml
```

- #### Create `kustomization` and add all resources
```
# kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# kubernetes resources to be managed by kustomize
resources:
  - db/db-config.yaml
  - db/db-depl.yaml
  - db/db-service.yaml
  - message-broker/rabbitmq-config.yaml
  - message-broker/rabbitmq-depl.yaml
  - message-broker/rabbitmq-service.yaml
  - nginx/nginx-depl.yaml
  - nginx/nginx-service.yaml
```

- #### Deploy using Kustomize
```
$ kubectl apply -k .
```

## Method 2

- #### Show directory
```
$ tree
.
├── db
│   ├── db-config.yaml
│   ├── db-depl.yaml
│   ├── db-service.yaml
│   └── kustomization.yaml
├── kustomization.yaml
├── message-broker
│   ├── kustomization.yaml
│   ├── rabbitmq-config.yaml
│   ├── rabbitmq-depl.yaml
│   └── rabbitmq-service.yaml
└── nginx
    ├── kustomization.yaml
    ├── nginx-depl.yaml
    └── nginx-service.yaml
```

- #### kustomization.yaml file for k8s/db:
```
resources:
  - db-depl.yaml
  - db-service.yaml
  - db-config.yaml
```

- #### kustomization.yaml file for k8s/message-broker:
```
resources:
  - rabbitmq-config.yaml
  - rabbitmq-depl.yaml
  - rabbitmq-service.yaml
```

- #### kustomization.yaml file for k8s/nginx:
```
resources:
  - nginx-depl.yaml
  - nginx-service.yaml
```

- #### Root kustomization.yaml file for k8s directory:
```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# kubernetes resources to be managed by kustomize

resources:
  - db/
  - message-broker/
  - nginx/
```

- #### Deploy using Kustomize
```
$ kubectl apply -k .
```