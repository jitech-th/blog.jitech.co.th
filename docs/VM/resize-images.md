---
tags:
    - Linux
    - KVM
    - Filesystem
---
# Resizing QCOW2 images

## Instructions

- #### list all filesystems, and verify the disk to be expanded
```
virt-filesystems --long -h --all -a olddisk.qcow2
# Name       Type        VFS   Label            MBR  Size  Parent
# /dev/sda1  filesystem  ntfs  System Reserved  -    50M   -
# /dev/sda2  filesystem  ntfs  -                -    39G   -
# /dev/sda3  filesystem  ntfs  -                -    513M  -
# /dev/sda1  partition   -     -                07   50M   /dev/sda
# /dev/sda2  partition   -     -                07   39G   /dev/sda
# /dev/sda3  partition   -     -                27   513M  /dev/sda
# /dev/sda   device      -     -                -    60G   -
```

- #### create a new disk
```
$ qemu-img create -f qcow2 -o preallocation=metadata newdisk.qcow2 50G
```

- #### resize the disk
```
$ virt-resize --expand /dev/sda2 olddisk.qcow2 newdisk.qcow2
```

## Note

- #### alternative way to expand
```
$ qemu-img resize disk_image +10G
```
but this way, you must use file system and partitioning tools inside the virtual machine to actually begin using the new space

take a look at [disk expansion](../../Linux/disk-expansion/)

for other commands [QEMU](https://wiki.archlinux.org/title/QEMU)