---
tags:
    - Docker
    - Docker runtimes
---
# Change container runtime in Docker

> Container runtime - it’s software that runs and manages the components required to run containers.

- **Open Container Initiative (OCI) Runtimes** - or low-level runtimes, implementations of the OCI Runtime spec are focused on managing the container lifecycle
  - Native runtimes - runc, crun, rkt
  - Sandboxed and Virtualized Runtimes - gVisor, katacontainer
- **Container Runtime Interface (CRI)** - containerd, CRI-O

By default, Docker uses containerd and runc

for more information [here](https://www.capitalone.com/tech/cloud/container-runtime/)

## Instructions 

- #### Edit `/etc/docker/daemon.json`
After runtime installation, add runtime to the file

```
# /etc/docker/daemon.json
{
  "default-runtime": "runc",
  "runtimes": {
    "kata-runtime": {
      "path": "/snap/bin/kata-containers.runtime"
    }
  }
}
```

- #### Restart docker

```
$ systemctl restart docker
```