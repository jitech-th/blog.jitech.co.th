---
tags:
    - Linux
    - KVM
---
# Access VM by its hostname

**This only works in KVM NAT mode**

## Instructions

- #### Add KVM DNS 

```
$ nmtui
# then add DNS at the first order
```

- #### Verify

```
$ ifconfig virbr0
virbr0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255
        ether 52:54:00:82:6a:76  txqueuelen 1000  (Ethernet)
        RX packets 90052  bytes 5630027 (5.3 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 145764  bytes 769552690 (733.9 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

# OR

$ virsh net-dhcp-leases default 
 Expiry Time           MAC address         Protocol   IP address          Hostname   Client ID or DUID
-----------------------------------------------------------------------------------------------------------
 2023-03-21 20:30:59   52:54:00:52:c0:b2   ipv4       192.168.122.97/24   VM         01:52:54:00:52:c0:b2

$ ping VM
PING VM (192.168.122.97) 56(84) bytes of data.
64 bytes from VM (192.168.122.97): icmp_seq=1 ttl=64 time=0.131 ms
64 bytes from VM (192.168.122.97): icmp_seq=2 ttl=64 time=0.055 ms
64 bytes from VM (192.168.122.97): icmp_seq=3 ttl=64 time=0.078 ms
...
```