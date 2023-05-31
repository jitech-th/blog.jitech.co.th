---
tags:
  - Linux
  - DHCP
---
# Set up DHCP server in Linux

Here we will use dhcpd as a dhcp server

## Instructions

- #### Install `dhcp-server`

```
$ dnf install dhcp-server
```

- #### Add global configurations to `/etc/dhcpd/dhcpd.conf`

First we will add global configurations. For example, DNS server, lease time, gateway, etc.
```
# Global configurations
option domain-name "admin.local";

option domain-name-servers 192.168.103.53;
default-lease-time 7200;
max-lease-time 7200;

option router 192.168.103.1
option subnet-mask 255.255.255.0;
option broadcast-address 192.168.103.255;
```

- #### Add configurations for a subnet

```
subnet 192.168.103.0 netmask 255.255.255.0 {
  range 192.168.103.100 192.168.103.200;
}
```

for more information [IBM](https://www.ibm.com/docs/en/tpmfod/7.1.1.14?topic=configuration-dhcpdconf-example) or [Redhat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/sec-dhcp-configuring-server)