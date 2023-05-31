---
tags:
    - Error
    - KVM
    - Image
---
# Resizing QCOW2 image errors

errors from [resize images](../../VM/resize-images/)

## Logs
```
$ virt-resize --expand /dev/sda2 AlmaLinux-8-GenericCloud-8.7-20221111.x86_64.qcow2 AlmaLinux-8.qcow2
virt-resize: error: libguestfs error: /usr/bin/supermin exited with error 
status 1.
To see full error messages you may need to enable debugging.
Do:
  export LIBGUESTFS_DEBUG=1 LIBGUESTFS_TRACE=1
and run the command again.  For further information, read:
  http://libguestfs.org/guestfs-faq.1.html#debugging-libguestfs
You can also run 'libguestfs-test-tool' and post the *complete* output
into a bug report or message to the libguestfs mailing list.

If reporting bugs, run virt-resize with debugging enabled and include the 
complete output:

  virt-resize -v -x [...]
```

## Solutions
```
$ chmod +r /boot/vmlinuz-*
```

for more information [here](https://askubuntu.com/questions/1046828/how-to-run-libguestfs-tools-tools-such-as-virt-make-fs-without-sudo)