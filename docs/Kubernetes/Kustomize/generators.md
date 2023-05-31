# Kustomize generators

> Kustomize has secretGenerator and configMapGenerator, which generate Secret and ConfigMap from files or literals.

## configMapGenerator

- #### Create a application.properties file
```
$ cat <<EOF >application.properties
FOO=Bar
EOF

$ cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: example-configmap-1
  files: # <-- [ files | envs | literals ]
  - application.properties
EOF
```

- #### Examine the generated configMap
```
$ kubectl kustomize ./
apiVersion: v1
data:
  application.properties: |
        FOO=Bar
kind: ConfigMap
metadata:
  name: example-configmap-1-8mbdf7882g
```

- #### Example deployment that uses a generated ConfigMap
```
# Create a application.properties file
cat <<EOF >application.properties
FOO=Bar
EOF

cat <<EOF >deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app
        image: my-app
        volumeMounts:
        - name: config
          mountPath: /config
      volumes:
      - name: config
        configMap:
          name: example-configmap-1
EOF

cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
configMapGenerator:
- name: example-configmap-1
  files:
  - application.properties
EOF
```

## secretGenerator
```
# Create a password.txt file
cat <<EOF >./password.txt
username=admin
password=secret
EOF

cat <<EOF >./kustomization.yaml
secretGenerator:
- name: example-secret-1
  files: # <-- [ files | literals ]
  - password.txt
EOF
```

**Note:** secretGenerator usage is like configMapGenerator