---
tags:
    - Linux
    - Security
---
# Restrict a ssh user using chroot

## What is chroot?

-  **chroot** (change root) คือการเปลี่ยน root directory สำหรับ ssh user
-  **chroot** ใช้สำหรับป้องกันไม่ให้ user เข้า-ออก ไป directory ที่ไม่ได้รับอนุญาตได้
-  เปรียบเหมือนเอา user ไปขังอยู่ในคุกที่เรียกว่า chrooted jail

## Instructions

### create ssh chroot jail

- #### create directory for the jail
```
$ mkdir -p /home/restricted
```

- #### copy required `/dev`
  ps. `mknod` is used for create (or copy) `/dev` files
```
$ ls -l /dev/{null,zero,stdin,stdout,stderr,random,tty}
crw-rw-rw- 1  100 root 1, 3 Jun 22  2018 /dev/null
crw-rw-rw- 1 root root 1, 8 Jun 22  2018 /dev/random
lrwxrwxrwx 1 root root   15 Jun 22  2018 /dev/stderr -> /proc/self/fd/2
lrwxrwxrwx 1 root root   15 Jun 22  2018 /dev/stdin -> /proc/self/fd/0
lrwxrwxrwx 1 root root   15 Jun 22  2018 /dev/stdout -> /proc/self/fd/1
crw-rw-rw- 1 root tty  5, 0 Feb 25 21:04 /dev/tty
crw-rw-rw- 1 root root 1, 5 Jun 22  2018 /dev/zero

$ mkdir -p /home/restricted/dev/		
$ cd /home/restricted/dev/
$ mknod -m 666 null c 1 3
$ mknod -m 666 tty c 5 0
$ mknod -m 666 zero c 1 5
$ mknod -m 666 random c 1 8
```

- #### set permission for the directory
```
$ chown root:root /home/restricted
$ chmod 0755 /home/restricted
```

### setup shell for chroot jail

- #### copy important binary files
```
$ mkdir -p /home/restricted/bin
$ cp /bin/bash /home/restricted/bin/
```

- #### list all required dependencies for the binary files
```
$ ldd bin/bash 
	linux-vdso.so.1 (0x0000007f83338000)
	libtinfo.so.6 => /lib64/libtinfo.so.6 (0x0000007f83177000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000007f83156000)
	libc.so.6 => /lib64/libc.so.6 (0x0000007f82fe0000)
	/lib/ld-linux-aarch64.so.1 (0x0000007f832fa000)
```

- #### copy the dependencies
```
$ mkdir lib64 lib 
$ cp -v /lib64/{libtinfo.so.6,libdl.so.2,libc.so.6} /home/restricted/lib64/
$ cp /lib/ld-linux-aarch64.so.1 /home/restricted/lib64/.
```

### create and configure SSH user

- #### create user and update account files
```
$ useradd runner
$ mkdir /home/restricted/etc
$ cp /etc/{passwd,group} /home/restricted/etc/
```

### configure SSH user to use chroot jail 

- #### edit `/etc/ssh/sshd_config`
```
$ vim /etc/ssh/sshd_config
```
add lines below in the file
```
#define username to apply chroot jail to
Match User runner
#specify chroot jail
ChrootDirectory /home/restircted
```

- #### restart sshd
```
$ systemctl restart sshd
```

## Extras

### setup SSH user to use shared storage

- #### mount bind existing shared directory to chroot jail 
```
$ mount --bind /mnt/shared /home/restricted/storage
```

- #### add fstab
```
$ /mnt/shared /home/restricted/storage none rw,bind 0 0 >> /etc/fstab
```

## For more about scp for chroot user
[using `strace` command](https://wararit.cc/Linux/strace)