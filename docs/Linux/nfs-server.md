---
tags:
    - Linux
    - Filesystem
    - NFS
---
# Setting up NFS server

## Package

- `nfs-utils`

## Instructions

### Server

- #### Install nfs package

```
$ dnf install nfs-utils
```

- #### Edit `/etc/exports` to allow specific IP on the shared directory 

```
/mnt/shared				192.168.2.11/24(rw,sync,no_all_squash,root_squash)
```

- #### Start nfs server

```
$ systemctl start nfs-server
$ systemctl enable nfs-server
```

- #### Export NFS

```
$ exportfs -arv
```

- #### Display exported nfs

```
$ exportfs -s
```

### Client

- #### Install nfs package

```
$ dnf install nfs-utils
```

- #### Show exported nfs server

```
$ showmount -e 192.168.2.11
Export list for 192.168.2.11:
/mnt/shared 192.168.2.0/26
``` 

- #### Edit fstab and mount

```
$ echo "10.20.20.8:/mnt/backups     /mnt/backups  nfs     defaults 0 0">>/etc/fstab
$ mount -a
```

## Reference

- [How to Set Up NFS Server and Client on CentOS 8](https://www.tecmint.com/install-nfs-server-on-centos-8/)
