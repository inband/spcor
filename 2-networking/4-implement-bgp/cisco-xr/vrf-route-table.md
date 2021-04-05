# route table

```
RP/0/RP0/CPU0:XR1#show bgp vpnv4 unicast summary 
Mon Apr  5 08:25:36.454 UTC
BGP router identifier 11.11.11.11, local AS number 123
BGP generic scan interval 60 secs
Non-stop routing is enabled
BGP table state: Active
Table ID: 0x0   RD version: 0
BGP main routing table version 1
BGP NSR Initial initsync version 1 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0
BGP scan interval 60 secs

BGP is operating in STANDALONE mode.


Process       RcvTblVer   bRIB/RIB   LabelVer  ImportVer  SendTblVer  StandbyVer
Speaker               1          1          1          1           1           0

Neighbor        Spk    AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down  St/PfxRcd
2.2.2.2           0   123    3222    2929        1    0    0    2d00h          0
12.12.12.12       0   123    2933    2929        1    0    0    2d00h          0
```

XR1 has 2 peers (both route-reflectors) but is not receiving Pfx.  There is no VRF defined or route-targets.


What is required? 

```
hostname XR1
domain name inband.io
!
vrf X
 rd 7:7
 address-family ipv4 unicast
  import route-target
   7:7
  !
  export route-target
   7:7
  !
 !
```

Now we are receiving prefixes but there are no routes in table.

```
RP/0/RP0/CPU0:XR1#show bgp vpnv4 unicast summary 
Mon Apr  5 08:27:04.984 UTC
BGP router identifier 11.11.11.11, local AS number 123
BGP generic scan interval 60 secs
Non-stop routing is enabled
BGP table state: Active
Table ID: 0x0   RD version: 0
BGP main routing table version 3
BGP NSR Initial initsync version 1 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0
BGP scan interval 60 secs

BGP is operating in STANDALONE mode.


Process       RcvTblVer   bRIB/RIB   LabelVer  ImportVer  SendTblVer  StandbyVer
Speaker               3          3          3          3           3           0

Neighbor        Spk    AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down  St/PfxRcd
2.2.2.2           0   123    3225    2931        3    0    0    2d00h          2
12.12.12.12       0   123    2937    2931        3    0    0    2d00h          2

```

No routes in table (vrf X)

```
RP/0/RP0/CPU0:XR1#show route vrf X ipv4 
Mon Apr  5 08:30:33.199 UTC

% No matching routes found
```

What is required?  Well perhaps we need an interface in vrf X? Well we may need this eventually to test reachability but the requirement is AFI/SAFI under BGP.


```
RP/0/RP0/CPU0:XR1(config)#router bgp 123
RP/0/RP0/CPU0:XR1(config-bgp)#vrf X
RP/0/RP0/CPU0:XR1(config-bgp-vrf)#address-family ipv4 unicast 
RP/0/RP0/CPU0:XR1(config-bgp-vrf-af)#commit
Mon Apr  5 08:32:01.580 UTC
RP/0/RP0/CPU0:XR1(config-bgp-vrf-af)#do show route vrf X         
Mon Apr  5 08:32:14.248 UTC

Codes: C - connected, S - static, R - RIP, B - BGP, (>) - Diversion path
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - ISIS, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, su - IS-IS summary null, * - candidate default
       U - per-user static route, o - ODR, L - local, G  - DAGR, l - LISP
       A - access/subscriber, a - Application route
       M - mobile route, r - RPL, t - Traffic Engineering, (!) - FRR Backup path

Gateway of last resort is not set

B    7.7.7.1/32 [200/0] via 1.1.1.1 (nexthop in vrf default), 00:00:10
B    7.7.7.3/32 [200/0] via 3.3.3.3 (nexthop in vrf default), 00:00:10
```

---

```
RP/0/RP0/CPU0:XR1#ping vrf X 7.7.7.1
Mon Apr  5 08:50:02.654 UTC
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 7.7.7.1, timeout is 2 seconds:
UUUUU
Success rate is 0 percent (0/5)
```


Ofcourse reachability is not going to work until an interface is defined.

```
RP/0/RP0/CPU0:XR1(config)#interface loopback 7
RP/0/RP0/CPU0:XR1(config-if)#vrf X  
RP/0/RP0/CPU0:XR1(config-if)#ipv4 address 7.7.7.11/32
RP/0/RP0/CPU0:XR1(config-if)#commit
```

This time it timeouts - this is because Lo7 is NOT advertised into BGP.

```
RP/0/RP0/CPU0:XR1#ping vrf X 7.7.7.1
Mon Apr  5 08:51:58.710 UTC
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 7.7.7.1, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```

Let advertise Lo7 ```7.7.7.11/32```

```
RP/0/RP0/CPU0:XR1(config)#router bgp 123
RP/0/RP0/CPU0:XR1(config-bgp)#vrf X
RP/0/RP0/CPU0:XR1(config-bgp-vrf)#address-family ipv
ipv4  ipv6  
RP/0/RP0/CPU0:XR1(config-bgp-vrf)#address-family ipv4 unicast 
RP/0/RP0/CPU0:XR1(config-bgp-vrf-af)#ne
network  nexthop  
RP/0/RP0/CPU0:XR1(config-bgp-vrf-af)#network 7.7.7.11?
A.B.C.D/length  
RP/0/RP0/CPU0:XR1(config-bgp-vrf-af)#network 7.7.7.11/32
RP/0/RP0/CPU0:XR1(config-bgp-vrf-af)#commit
```

Now test reachability


```
RP/0/RP0/CPU0:XR1#ping vrf X 7.7.7.1
Mon Apr  5 08:54:34.179 UTC
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 7.7.7.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/11/45 ms
```

