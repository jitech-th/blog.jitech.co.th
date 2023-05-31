---
tags:
    - Networking
---
# Cisco default route propagation via OSPF

We have OSPF running on our network, all nodes can communicate with each other.

Next what should we do if we want all of our nodes can access the internet?

We can do **Default route**, but with default route we need to do default route propagation, so they all will have the route on their routing table.

for [OSPF routing](../ospf-routing)

## Topology

![topology](../img/GNS3-topology.png)

## Instructions

#### Core router
```
Core(config-router)#ip route 0.0.0.0 0.0.0.0 192.168.122.1 #<-- address of F0/0
Core(config-router)#router ospf 1
Core(config-router)#default-information originate
Core#sh ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 192.168.122.1 to network 0.0.0.0

C    192.168.122.0/24 is directly connected, FastEthernet0/0
O    192.168.102.0/24 [110/2] via 192.168.2.2, 00:46:03, FastEthernet1/1
C    192.168.1.0/24 is directly connected, FastEthernet1/0
O    192.168.103.0/24 [110/2] via 192.168.3.2, 00:41:04, FastEthernet2/0
C    192.168.2.0/24 is directly connected, FastEthernet1/1
C    192.168.3.0/24 is directly connected, FastEthernet2/0
O    192.168.101.0/24 [110/2] via 192.168.1.2, 00:44:13, FastEthernet1/0
S*   0.0.0.0/0 [1/0] via 192.168.122.1
```

#### verify routing table

```
RedTeam#show ip route       
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 192.168.2.1 to network 0.0.0.0

C    192.168.102.0/24 is directly connected, FastEthernet0/1
O    192.168.1.0/24 [110/2] via 192.168.2.1, 01:04:49, FastEthernet0/0
O    192.168.103.0/24 [110/3] via 192.168.2.1, 00:59:46, FastEthernet0/0
C    192.168.2.0/24 is directly connected, FastEthernet0/0
O    192.168.3.0/24 [110/2] via 192.168.2.1, 01:04:49, FastEthernet0/0
O    192.168.101.0/24 [110/3] via 192.168.2.1, 01:03:05, FastEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 192.168.2.1, 00:15:44, FastEthernet0/0
```

that is all we need to do, simple?