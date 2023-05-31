---
tags:
    - Linux
    - KVM
    - Filesystem
---
# Convert image format

## Convert VMDK to QCOW2

### Option 1 - with `vmx`
There're several files those are related to an ESXI image:

- `vmx` a template of the VM (can be compared to `.xml` for KVM) 
- `vmdk` a **real** image file (can be compared to `.qcow2` for KVM) 

if we have vmx and vmdk, the below command is recommended for image convertion

```
$ virt-v2v -i vmx </path/to/.vmx> -o local -of qcow2 -os </path/to/des_dir>
```

we will get both a template file and image(s) (depends on how many disk our VM uses)

### Option 2 - without `vmx`
```
$ qemu-img convert -p -f vmdk -O qcow2 </path/to/.vmdk> </path/to/.qcow2>
```

## Convert VDI to QCOW2

Since qemu-img (at least in my environment) doesn't support VDI, so VBoxManage will be used.

- #### Import Virtualbox repo and install dependencies

```
$ wget https://download.virtualbox.org/virtualbox/rpm/el/virtualbox.repo
$ mv virtualbox.repo /etc/yum.repos.d/
$ dnf -y install binutils kernel-devel kernel-headers libgomp make patch gcc glibc-headers glibc-devel dkms
```

- #### Install Virtualbox

```
$ dnf install -y VirtualBox-7.0
```

- #### Convert VDI to VMDK

```
$ VBoxManage clonehd --format vmdk SEED-Ubuntu20.04.vdi seed-ubuntu20.04.vmdk
```

- #### Convert VMDK to QCOW2

```
$ qemu-img convert -f vmdk -O qcow2 seed-ubuntu20.04.vmdk seed-ubuntu20.04.qcow2 
```