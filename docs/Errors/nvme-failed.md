---
tags:
    - Linux
    - Error
---

# NVME failed

## Error logs

```
[8.218341] nvme nvme0: pci function 0000:01:00.0
[8.221597] nvme nvme0: Shutdown timeout set to 10 seconds
[8.228517] nvme nvme0: 12/0/0 default/read/poll queues
[8.231876] nvme0n1: detected capacity change from 0 to 1000204886016
[39.392032] nvme nvme0: controller is down; will reset: CSTS=0xffffffff, PCI_STATUS=0x10
[39.4140411] nvme 0000:01:00.0: enabling device (0000->0002)
[39.414106] nvme nvme0: Removing after probe failure status: -19
```

## Instructions

- #### Find the line that starts with `GRUB_CMDLINE_LINUX_DEFAULT` and add the parameter there. 
Your line should look like this now (Make sure it is a single line with no line break)

`GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvme_core.default_ps_max_latency_us=5500"`

- #### update grub config

```
$ grub2-mkconfig -o /boot/grub2/grub.cfg
```

- #### reboot

```
$ reboot
```
