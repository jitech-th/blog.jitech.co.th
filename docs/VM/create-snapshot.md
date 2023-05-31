---
tags:
    - Linux
    - KVM
    - Snapshot
---
# Create a VM snapshot

```
$ virsh dumpxml --domain ubuntu-seed20.04
..
<devices>
    <emulator>/usr/libexec/qemu-kvm</emulator>
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='/home/images/seed-ubuntu20.04-gui.qcow2'/>
      <target dev='vda' bus='virtio'/>
      <address type='pci' domain='0x0000' bus='0x04' slot='0x00' function='0x0'/>
    </disk>
..
```

```
$ virsh snapshot-create-as --domain ubuntu-seed20.04 --name test --disk-only --diskspec vda,snapshot=external,file=/home/images/test.qcow2 
```

```
$ qemu-img info /home/images/test.qcow2 
image: /home/images/test.qcow2
file format: qcow2
virtual size: 80 GiB (85899345920 bytes)
disk size: 196 KiB
cluster_size: 65536
backing file: /home/images/seed-ubuntu20.04-gui.qcow2
backing file format: qcow2
Format specific information:
    compat: 1.1
    compression type: zlib
    lazy refcounts: false
    refcount bits: 16
    corrupt: false
    extended l2: false
```

