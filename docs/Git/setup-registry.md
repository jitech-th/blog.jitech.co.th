# Setup Gitlab container registry

## Instructions

- #### generate access token with read_registry & write-registry policy

- #### login to the registry
```
$ docker login registry.gitlab.com -u <username> -p <token>
```

- #### build an image and tag it
```
$ docker build -t registry.gitlab.com/<username>/<registry-name>/<image-name> -f Dockerfile .
```

- #### push to the registry
```
$ docker push registry.gitlab.com/<username>/<registry-name>/<image-name>
```