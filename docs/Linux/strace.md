---
tags:
    - Linux
    - Debug
    - strace
---

# Use strace to debug commands

## What is strace?
- `strace` ใช้สำหรับ trace ดูว่า command เรียกใช้ lib อะไรบ้าง

## Instructions

- #### install `strace`
```
$ dnf install strace
```

- #### debug scp in chroot errors
```
$ scp ....
unknown user 1000
lost connection
```

- #### try using `strace` to identify missing libraries
```
$ strace scp
execve("/usr/bin/scp", ["scp"], 0x7fd2e609d0 /* 27 vars */) = 0
brk(NULL)                               = 0x55a8fb6000
faccessat(AT_FDCWD, "/etc/ld.so.preload", R_OK) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=15931, ...}) = 0
mmap(NULL, 15931, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f9f5e2000
close(3)                                = 0
openat(AT_FDCWD, "/lib64/libcrypto.so.1.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0\267\0\1\0\0\0\0\320\7\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=2815088, ...}) = 0
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f9f5e0000
mmap(NULL, 2850040, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f9f2f2000
mprotect(0x7f9f566000, 73728, PROT_NONE) = 0
mmap(0x7f9f578000, 188416, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x276000) = 0x7f9f578000
mmap(0x7f9f5a6000, 15608, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f9f5a6000
close(3)  
...
..
openat(AT_FDCWD, "/lib64/libnss_files.so.2", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0\267\0\1\0\0\0\300$\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=70544, ...}) = 0
mmap(NULL, 157000, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f91fb7000
mprotect(0x7f91fc3000, 77824, PROT_NONE) = 0
mmap(0x7f91fd6000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0xf000) = 0x7f91fd6000
mmap(0x7f91fd8000, 21832, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f91fd8000
close(3)   
```

- #### and I found `/lib64/libnss_files.so.2` is missing
then just copy to chroot `lib64` and it works! 