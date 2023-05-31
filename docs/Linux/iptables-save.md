---
tags:
    - Linux
    - Security
    - Iptables
---
# Save and restore iptables rules

`iptables` is a linux firewall tool. By default, the rules don't persist when the server is rebooted. So, here we are

## Instructions

### Method 1: Save and restore manaully

- #### Save the current iptables rules

```
$ iptables-save > /etc/iptables
```

- #### Add this line to `/etc/rc.local`

To run the command on boot
```
# in /etc/rc.local
$ iptables-restore < /etc/iptables
```

### Method 2: Use `iptables-services`

- #### Install iptables-service

```
$ dnf install iptables-services
```

- #### Save the current iptables rules

```
$ service iptables save
```

- #### Start and enable iptables service

```
$ systemctl start iptables
$ systemctl enable iptables
```

Now, our rules will persist when rebooting.