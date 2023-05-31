---
tags:
    - Networking
---
# Configure NAT on Cisco routers

3 modes of NAT:

1. Overloading or Port Address Translation (PAT)
2. Static Port Address Translation (Port Redirection)
3. Configuring Static NAT

an address may be one of four types:

1. **Inside local** addresses are assigned to inside devices. These addresses are not advertised to the outside.
2. **Inside global** are addresses by which inside devices are known to the outside.
3. **Outside local** are addresses by which outside devices are known to the inside.
4. **Outside global** addresses are assigned to outside devices. These addresses are not advertised to the inside.

here, we need all of our internal IPs natting to a single IP, so we choose option 1

## Topology

![topology](../img/GNS3-topology.png)

## Instructions

#### Core router

```
Core(config)#interface Fa 0/0
Core(config-if)#ip nat outside

Core(config)#interface Fa 1/0
Core(config-if)#ip nat inside
Core(config)#interface Fa 1/1
Core(config-if)#ip nat inside
Core(config)#interface Fa 2/0
Core(config-if)#ip nat inside

Core(config)#access-list 100 permit 192.168.0.0 0.0.255.255 any
Core(config)#ip nat inside source list 100 interface FastEthernet0/0 overload
```
#### verify
```
Core#show ip nat translations 
Pro Inside global      Inside local       Outside local      Outside global
udp 192.168.122.182:34617 192.168.101.12:34617 203.131.222.11:123 203.131.222.11:123
udp 192.168.122.182:34970 192.168.101.12:34970 203.159.70.33:123 203.159.70.33:123
udp 192.168.122.182:35179 192.168.101.12:35179 203.159.70.33:123 203.159.70.33:123
udp 192.168.122.182:35869 192.168.101.12:35869 203.131.222.11:123 203.131.222.11:123
udp 192.168.122.182:36430 192.168.101.12:36430 202.29.58.130:123 202.29.58.130:123
udp 192.168.122.182:37205 192.168.101.12:37205 162.159.200.1:123 162.159.200.1:123
udp 192.168.122.182:38001 192.168.101.12:38001 61.91.205.20:123 61.91.205.20:123
udp 192.168.122.182:38530 192.168.101.12:38530 61.91.205.20:123 61.91.205.20:123
udp 192.168.122.182:39580 192.168.101.12:39580 162.159.200.1:123 162.159.200.1:123
udp 192.168.122.182:41781 192.168.101.12:41781 203.131.222.11:123 203.131.222.11:123
udp 192.168.122.182:41784 192.168.101.12:41784 61.91.205.20:123 61.91.205.20:123
udp 192.168.122.182:43904 192.168.101.12:43904 61.91.205.20:123 61.91.205.20:123
udp 192.168.122.182:44554 192.168.101.12:44554 61.91.205.20:123 61.91.205.20:123
udp 192.168.122.182:45997 192.168.101.12:45997 61.91.205.20:123 61.91.205.20:123
 --More-- 
```
