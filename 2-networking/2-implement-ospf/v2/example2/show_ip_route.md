# show ip route


```
CSR1#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

      192.51.100.0/24 is variably subnetted, 12 subnets, 3 masks
S        192.51.100.0/24 is directly connected, Null0
C        192.51.100.1/32 is directly connected, Loopback0
O        192.51.100.2/32 
           [110/2] via 192.51.100.201, 21:48:56, GigabitEthernet2.12
O        192.51.100.3/32 
           [110/2] via 192.51.100.203, 00:29:22, GigabitEthernet3.13
O        192.51.100.4/32 
           [110/3] via 192.51.100.203, 00:28:06, GigabitEthernet3.13
           [110/3] via 192.51.100.201, 00:28:06, GigabitEthernet2.12
C        192.51.100.5/32 is directly connected, Loopback5
C        192.51.100.200/31 is directly connected, GigabitEthernet2.12
L        192.51.100.200/32 is directly connected, GigabitEthernet2.12
C        192.51.100.202/31 is directly connected, GigabitEthernet3.13
L        192.51.100.202/32 is directly connected, GigabitEthernet3.13
O        192.51.100.204/31 
           [110/2] via 192.51.100.201, 00:30:19, GigabitEthernet2.12
O        192.51.100.206/31 
           [110/2] via 192.51.100.203, 00:31:21, GigabitEthernet3.13

```

Note the equal cost route to **192.51.100.4** whicjh is Loopback0 on CSR4

**show ip route** *dst-ip-address*

```
CSR1#show ip route 192.51.100.4                
Routing entry for 192.51.100.4/32
  Known via "ospf 1", distance 110, metric 3, type intra area
  Last update from 192.51.100.201 on GigabitEthernet2.12, 00:32:45 ago
  Routing Descriptor Blocks:
  * 192.51.100.203, from 192.51.100.4, 00:32:45 ago, via GigabitEthernet3.13
      Route metric is 3, traffic share count is 1
    192.51.100.201, from 192.51.100.4, 00:32:45 ago, via GigabitEthernet2.12
      Route metric is 3, traffic share count is 1
```

**show ip cef** *dst-ip-address*  **detail**

```
CSR1#show ip cef 192.51.100.4 detail 
192.51.100.4/32, epoch 2, per-destination sharing
  nexthop 192.51.100.201 GigabitEthernet2.12
  nexthop 192.51.100.203 GigabitEthernet3.13

```

**traceroute** *dst-ip-address* [**numeric**] [**source** {*inferace*|*ip-address*}]

```
CSR1#traceroute 192.51.100.4 numeric source Lo0
Type escape sequence to abort.
Tracing the route to 192.51.100.4
VRF info: (vrf in name/id, vrf out name/id)
  1 192.51.100.201 1 msec
    192.51.100.203 2 msec
    192.51.100.201 1 msec
  2 192.51.100.207 2 msec
    192.51.100.205 1 msec * 
```



