---
tags:
    - Linux
    - KVM
    - Snapshot
---
# Merge VM snapshots

## Problems
- vm เก็บ snapshot เป็น change เมื่อทำ snapshot ไปเรื่อยๆ จะพบว่า มีไฟล์ snapshot เล็กๆ เต็มไปหมด
- ดังนั้นการ merge จะช่วยรวมไฟล์เล็กๆ นั้นให้เป็นไฟล์เดียว

## Instructions

- #### verify the backing chain of the disk image
```
$ qemu-img info --backing-chain test1-snapshot 
image: test1-snapshot
file format: qcow2
virtual size: 16G (17179869184 bytes)
disk size: 4.6G
cluster_size: 65536
backing file: /var/lib/libvirt/images/test1-base
backing file format: qcow2
Format specific information:
    compat: 0.10
    refcount bits: 16
image: /var/lib/libvirt/images/test1-base ***
file format: qcow2
virtual size: 16G (17179869184 bytes)
disk size: 1.6G
cluster_size: 65536
Format specific information:
    compat: 1.1
    lazy refcounts: false
    refcount bits: 16
    corrupt: false
```

- #### backup the base image
```
$ cp test1-base new-base
```

- #### change the backing image
```
$ qemu-img rebase -F qcow2 -b new-base test1-snapshot
```

- #### commit changes 
```
$ qemu-img commit test1-snapshot
Image committed.
$ qemu-img info new-base 
image: new-base
file format: qcow2
virtual size: 16G (17179869184 bytes)
disk size: 6.1G
cluster_size: 65536
Format specific information:
    compat: 1.1
    lazy refcounts: false
    refcount bits: 16
    corrupt: false
```

ref: [Qemu: Merge snapshot and backing file into standalone disk](https://medium.com/@kumar_pravin/qemu-merge-snapshot-and-backing-file-into-standalone-disk-c8d3a2b17c0e)