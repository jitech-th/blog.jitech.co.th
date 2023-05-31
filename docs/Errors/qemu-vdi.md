---
tags:
    - Error
    - VM
    - Image
---
# VDI is not supported for qemu-img

I tried to convert vdi image to qcow2 image and I got this error

```
$ qemu-img convert -f vdi -O qcow2 SEED-Ubuntu20.04.vdi seed-ubuntu20.04.qcow2
qemu-img: Could not open 'SEED-Ubuntu20.04.vdi': Unknown driver 'vdi'
```

As you can see, vdi is not supported by qeme-img

```
$ qemu-img --help
...
Supported formats: blkdebug blklogwrites blkverify compress copy-before-write copy-on-read file ftp ftps gluster host_cdrom host_device http https iscsi iser luks nbd null-aio null-co nvme preallocate qcow2 quorum raw rbd ssh throttle vhdx vmdk vpc
```

So, we need to convert it to vmdk first then qcow2, using VBoxManage

please take a look at [here](../../VM/convert-images.md)