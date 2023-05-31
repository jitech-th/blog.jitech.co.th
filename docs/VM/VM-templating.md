---
tags:
    - Linux
    - KVM
    - Snapshot
---
# Clean VM for a template

## In the guest machine

- #### Clear hostname and machine id

```
$ rm /etc/machine-id && rm /etc/hostname
```

- #### Clear logs, history and shutdown

```
$ truncate -s 0 /var/log/*.log
$ history -c && cat /dev/null > .bash_history && shutdown
```

## In the host machine

- #### Set hostname of the VM
  
```
$ virt-customize -a /home/images/test.qcow2 --hostname test-ubuntu
```

- #### Generate a new machine id
After we deleted `/etc/machine-id` then `systemd-journald.service` will be unable to start. We can fix it by generating a new machine id

```
$ systemd-machine-id-setup
```

- #### Reboot
```
$ reboot
```