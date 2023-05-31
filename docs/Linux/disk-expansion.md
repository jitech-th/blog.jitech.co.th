---
tags:
    - Linux
    - Filesystem
---
# Disk Partitions

## Commands

There're several commands to be used for working with partitions:

1. `df`/`lsblk` - to list mounted path and available disks.

2. `mount`/`umount` - to mount or unmount a partition.

3. `fdsik` - partition management tool

4. `e2fsck` - partition error checker

5. `resize2fs` - to modify existing filesystem to fit new size bound

## Instruction

### df and lsblk

```
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        20G  5.5G   15G  28% /
devtmpfs        891M     0  891M   0% /dev
tmpfs           925M     0  925M   0% /dev/shm
tmpfs           925M   37M  888M   4% /run
tmpfs           925M     0  925M   0% /sys/fs/cgroup
/dev/mmcblk0p1  286M   92M  195M  32% /boot
...
```

```
$ lsblk -f
NAME        FSTYPE LABEL UUID                                 MOUNTPOINT
sda                                                           
`-sda1      ext4         8884f6c3-d495-4bbb-a45a-ac35965f595a /mnt/shared
mmcblk0                                                       
|-mmcblk0p1 vfat         AF4E-6C7B                            /boot
|-mmcblk0p2 swap   _swap 16b4b530-6449-48f8-8974-7f358793f904 
`-mmcblk0p3 ext4   _/    e233afa5-9aae-4036-9e68-f2cfd1814994 /
...
```

- `mmvblk0` - the whole device

- `mmcblk0p1,2,..` - the partitions

### umount

```
$ umount <mounting_point>
```

### fdisk

```
$ fdisk /dev/sda

Command (m for help): p

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     9437183     4717568   83  Linux

Command (m for help): d
Selected partition 1

Command (m for help): p

   Device Boot      Start         End      Blocks   Id  System

Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759

Created a new partition 1 of type 'Linux' and of size 10 GiB.
Partition #1 contains a ext4 signature.

Do you want to remove the signature? [Y]es/[N]o: N

Command (m for help): p

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

### e2fsck

```
$ e2fsck -f /dev/sda1
```

### resize2fs

```
$ resize2fs /dev/sda1
```

### mount

append `/dev/sda1 /mnt/shared auto noatime,nofail 0 0` to `/etc/fstab`

```
$ mount -a
```

### check

```
$ fs -h
...
/dev/sda1       117G  2.5M  111G   1% /mnt/shared
tmpfs           185M     0  185M   0% /run/user/0
```
