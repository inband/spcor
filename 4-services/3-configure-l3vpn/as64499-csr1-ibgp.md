# configure l3vpn for as64499 on csr1 and csr4 (ibgp)

Here CSR1 and CSR4 will be setup for L3VPN.  

* MPLS is already setup on CSR1, CSR2, CSR3 and CSR4
* CSR1 and CSR4 will be provider edge (PE)
* CSR2 and CSR3 will be provider (P) and remain BGP free

```vrf AS64499``` will be for Internet connectivity.  


For this example I temporary shut Gi2 and Gi3 on CSR3 so I can monitor flow.

```
CSR3#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
CSR3(config)#int Gi2
CSR3(config-if)#shut
CSR3(config-if)#int Gi3
CSR3(config-if)#shut   
```



Flow from CSR1 to CSR4 will traverse CSR2
```
CSR1 --- CSR2 --- CSR4
```

Verify reachability
```
CSR1#traceroute 192.51.100.4 source Lo0 numeric 
Type escape sequence to abort.
Tracing the route to 192.51.100.4
VRF info: (vrf in name/id, vrf out name/id)
  1 192.51.100.201 [MPLS: Label 16 Exp 0] 1 msec 2 msec 1 msec
  2 192.51.100.205 2 msec *  3 msec
```


Configured iBGP with ```vrf AS64499```


CSR1

```
hostname CSR1
!
!
vrf definition AS64499
 rd 64499:1
 !
 address-family ipv4
  route-target export 64499:1
  route-target import 64499:1
 exit-address-family
!
!
interface Loopback1
 vrf forwarding AS64499
 ip address 192.51.100.1 255.255.255.255
!
router bgp 64499
 bgp router-id 192.51.100.1
 bgp log-neighbor-changes
 network 192.51.100.0
 neighbor 192.51.100.4 remote-as 64499
 neighbor 192.51.100.4 update-source Loopback1
 !
ip route vrf AS64499 192.51.100.0 255.255.255.0 Null0

```

CSR4

```
hostname CSR4
!
!
vrf definition AS64499
 rd 64499:1
 !
 address-family ipv4
  route-target export 64499:1
  route-target import 64499:1
 exit-address-family
!
!
interface Loopback1
 vrf forwarding AS64499
 ip address 192.51.100.4 255.255.255.255
!
router bgp 64499
 bgp router-id 192.51.100.4
 bgp log-neighbor-changes
 network 192.51.100.0
 neighbor 192.51.100.1 remote-as 64499
 neighbor 192.51.100.1 update-source Loopback1
 !
ip route vrf AS64499 192.51.100.0 255.255.255.0 Null0

```

Verify that this has created a IPv4 BGP peering session

```
CSR4(config-router)#do show ip bgp summary                 
BGP router identifier 192.51.100.4, local AS number 64499
BGP table version is 2, main routing table version 2
1 network entries using 248 bytes of memory
1 path entries using 120 bytes of memory
1/1 BGP path/bestpath attribute entries using 248 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 616 total bytes of memory
BGP activity 1/0 prefixes, 1/0 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
192.51.100.1    4        64499      10      10        2    0    0 00:05:25        1

```


Now configure CSR1 and CSR4 for L3VPN also known as VPNv4.

First CSR1

```
CSR1(config-router)# address-family vpnv4
CSR1(config-router-af)#neighbor 192.51.100.4 send-community extended 
% Activate the neighbor for the address family first
            
CSR1(config-router-af)#neighbor 192.51.100.4 activate 
CSR1(config-router-af)#neighbor 192.51.100.4 send-community extended 
```

Here is the ```show run``` for CSR1

```
router bgp 64499
!
!
 address-family vpnv4
  neighbor 192.51.100.4 activate
  neighbor 192.51.100.4 send-community extended
 exit-address-family
 !
```

CSR4

```
router bgp 64499
!
 !
 address-family vpnv4
  neighbor 192.51.100.1 activate
  neighbor 192.51.100.1 send-community extended
 exit-address-family
 !
```

Verify L3VPN is enabled.  

Note lines:
```
Address family VPNv4 Unicast: advertised and received
 For address family: VPNv4 Unicast
  Session: 192.51.100.4
```

Full output:

```
CSR1#show ip bgp neighbors 192.51.100.4
BGP neighbor is 192.51.100.4,  remote AS 64499, internal link
  BGP version 4, remote router ID 192.51.100.4
  BGP state = Established, up for 00:09:12
  Last read 00:00:50, last write 00:00:46, hold time is 180, keepalive interval is 60 seconds
  Neighbor sessions:
    1 active, is not multisession capable (disabled)
  Neighbor capabilities:
    Route refresh: advertised and received(new)
    Four-octets ASN Capability: advertised and received
    Address family IPv4 Unicast: advertised and received
    Address family VPNv4 Unicast: advertised and received
    Enhanced Refresh Capability: advertised and received
    Multisession Capability: 
    Stateful switchover support enabled: NO for session 1
  Message statistics:
    InQ depth is 0
    OutQ depth is 0
    
                         Sent       Rcvd
    Opens:                  1          1
    Notifications:          0          0
    Updates:                4          4
    Keepalives:            11         10
    Route Refresh:          0          0
    Total:                 16         15
  Default minimum time between advertisement runs is 0 seconds

 For address family: IPv4 Unicast
  Session: 192.51.100.4
  BGP table version 2, neighbor version 2/0
  Output queue size : 0
  Index 3, Advertise bit 0
  3 update-group member
  Slow-peer detection is disabled
  Slow-peer split-update-group dynamic is disabled
  Interface associated: (none)
                                 Sent       Rcvd
  Prefix activity:               ----       ----
    Prefixes Current:               1          0
    Prefixes Total:                 1          0
    Implicit Withdraw:              0          0
    Explicit Withdraw:              0          0
    Used as bestpath:             n/a          0
    Used as multipath:            n/a          0

                                   Outbound    Inbound
  Local Policy Denied Prefixes:    --------    -------
    Total:                                0          0
  Number of NLRIs in the update sent: max 1, min 0
  Last detected as dynamic slow peer: never
  Dynamic slow peer recovered: never
  Refresh Epoch: 1
  Last Sent Refresh Start-of-rib: never
  Last Sent Refresh End-of-rib: never
  Last Received Refresh Start-of-rib: never
  Last Received Refresh End-of-rib: never
				       Sent	  Rcvd
	Refresh activity:	       ----	  ----
	  Refresh Start-of-RIB          0          0
	  Refresh End-of-RIB            0          0

 For address family: VPNv4 Unicast
  Session: 192.51.100.4
  BGP table version 2, neighbor version 2/0
  Output queue size : 0
  Index 1, Advertise bit 0
  1 update-group member
  Slow-peer detection is disabled
  Slow-peer split-update-group dynamic is disabled
  Interface associated: (none)
                                 Sent       Rcvd
  Prefix activity:               ----       ----
    Prefixes Current:               1          0
    Prefixes Total:                 1          0
    Implicit Withdraw:              0          0
    Explicit Withdraw:              0          0
    Used as bestpath:             n/a          0
    Used as multipath:            n/a          0

                                   Outbound    Inbound
  Local Policy Denied Prefixes:    --------    -------
    Total:                                0          0
  Number of NLRIs in the update sent: max 1, min 0
  Last detected as dynamic slow peer: never
  Dynamic slow peer recovered: never
  Refresh Epoch: 1
  Last Sent Refresh Start-of-rib: never
  Last Sent Refresh End-of-rib: never
  Last Received Refresh Start-of-rib: never
  Last Received Refresh End-of-rib: never
				       Sent	  Rcvd
	Refresh activity:	       ----	  ----
	  Refresh Start-of-RIB          0          0
          Refresh End-of-RIB            0          0

  Address tracking is enabled, the RIB does have a route to 192.51.100.4
  Connections established 3; dropped 2
  Last reset 00:09:13, due to Peer closed the session of session 1
  Transport(tcp) path-mtu-discovery is enabled
  Graceful-Restart is disabled
Connection state is ESTAB, I/O status: 1, unread input bytes: 0            
Connection is ECN Disabled, Mininum incoming TTL 0, Outgoing TTL 255
Local host: 192.51.100.1, Local port: 179
Foreign host: 192.51.100.4, Foreign port: 41995
Connection tableid (VRF): 0
Maximum output segment queue size: 50

Enqueued packets for retransmit: 0, input: 0  mis-ordered: 0 (0 bytes)

Event Timers (current time is 0x20B5DD):
Timer          Starts    Wakeups            Next
Retrans            13          0             0x0
TimeWait            0          0             0x0
AckHold            14         11             0x0
SendWnd             0          0             0x0
KeepAlive           0          0             0x0
GiveUp              0          0             0x0
PmtuAger            0          0             0x0
DeadWait            0          0             0x0
Linger              0          0             0x0
ProcessQ            0          0             0x0

iss: 3600179877  snduna: 3600180349  sndnxt: 3600180349
irs: 2858351662  rcvnxt: 2858352058
          
sndwnd:  15913  scale:      0  maxrcvwnd:  16384
rcvwnd:  15989  scale:      0  delrcvwnd:    395

SRTT: 824 ms, RTTO: 2094 ms, RTV: 1270 ms, KRTT: 0 ms
minRTT: 2 ms, maxRTT: 1000 ms, ACK hold: 200 ms
uptime: 552976 ms, Sent idletime: 46324 ms, Receive idletime: 46119 ms 
Status Flags: passive open, gen tcbs
Option Flags: nagle, path mtu capable
IP Precedence value : 6

Datagrams (max data segment is 9152 bytes):
Rcvd: 29 (out of order: 0), with data: 15, total data bytes: 395
Sent: 28 (retransmit: 0, fastretransmit: 0, partialack: 0, Second Congestion: 0), with data: 14, total data bytes: 471

 Packets received in fast path: 0, fast processed: 0, slow path: 0
 fast lock acquisition failures: 0, slow path: 0
TCP Semaphore      0x7F9B19D746C0  FREE 

```



CSR1 has eBGP peer with CSR10 AS64496

Bring up that session and check CSR4 RIB for ```vrf AS64499```



```
interface GigabitEthernet4.10
 encapsulation dot1Q 10
 vrf forwarding AS64499
 ip address 192.51.100.11 255.255.255.254
end
!
!
router bgp 64499
 bgp router-id 192.51.100.1
 bgp log-neighbor-changes
 network 192.51.100.0
 neighbor 192.51.100.4 remote-as 64499
 neighbor 192.51.100.4 update-source Loopback1
 !
 address-family vpnv4
  neighbor 192.51.100.4 activate
  neighbor 192.51.100.4 send-community extended
 exit-address-family
 !
 address-family ipv4 vrf AS64499
  network 192.51.100.0
  neighbor 192.51.100.10 remote-as 64496
  neighbor 192.51.100.10 activate
  neighbor 192.51.100.10 prefix-list PL_EBGP_OUT out
 exit-address-family

```


Check RIB on CSR4


```
CSR4#show ip route vrf AS64499

Routing Table: AS64499
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

B     192.0.2.0/24 [200/0] via 192.51.100.1, 00:00:35
      192.51.100.0/24 is variably subnetted, 2 subnets, 2 masks
S        192.51.100.0/24 is directly connected, Null0
C        192.51.100.4/32 is directly connected, Loopback1
```

Check Inter-AS reachability from CSR4 ```192.51.100.4``` to CSR10 ```192.0.2.10``` 

```
CSR4#ping vrf AS64499 192.0.2.10 source Lo1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.4 
.....
Success rate is 0 percent (0/5)

```


Well that failed - that is because CSR1 and CS4 are not advertising their Lo1's to each other as seen in CSR's RIB above.

CSR1's RIB

```
CSR1#show ip route vrf AS64499 

Routing Table: AS64499
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

B     192.0.2.0/24 [20/0] via 192.51.100.10, 00:08:32
      192.51.100.0/24 is variably subnetted, 4 subnets, 3 masks
S        192.51.100.0/24 is directly connected, Null0
C        192.51.100.1/32 is directly connected, Loopback1
C        192.51.100.10/31 is directly connected, GigabitEthernet4.10
L        192.51.100.11/32 is directly connected, GigabitEthernet4.10
CSR1#
```

To fix this ```redistribute connected``` on both CSR1 and CSR4

```
router bgp 64499
 !
 address-family ipv4 vrf AS64499
  redistribute connected
```


Check Inter-AS reachability from CSR4 ```192.51.100.4``` to CSR10 ```192.0.2.10``` 

Now it is reachable.  

```
CSR4#ping vrf AS64499 192.0.2.10 source Lo1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.4 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/5 ms
```


Traceroute

Note L3VPN label depth of 2
MPLS label is 18
VPNv4 label is 21


```
CSR4#traceroute vrf AS64499 192.0.2.10 source Lo1 numeric
Type escape sequence to abort.
Tracing the route to 192.0.2.10
VRF info: (vrf in name/id, vrf out name/id)
  1 192.51.100.204 [MPLS: Labels 18/21 Exp 0] 3 msec 2 msec 3 msec
  2 192.51.100.11 [MPLS: Label 21 Exp 0] 2 msec 1 msec 2 msec
  3 192.51.100.10 3 msec *  3 msec

```

Show CEF 

```
CSR4#show ip cef vrf AS64499 192.0.2.10 detail 
192.0.2.0/24, epoch 0, flags [rib defined all labels]
  recursive via 192.51.100.1 label 21
    nexthop 192.51.100.204 GigabitEthernet2.24 label 18

```



Show CSR4 RIB now

```
CSR4#show ip route vrf AS64499                           

Routing Table: AS64499
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

B     192.0.2.0/24 [200/0] via 192.51.100.1, 00:14:33
      192.51.100.0/24 is variably subnetted, 4 subnets, 3 masks
S        192.51.100.0/24 is directly connected, Null0
B        192.51.100.1/32 [200/0] via 192.51.100.1, 00:02:16
C        192.51.100.4/32 is directly connected, Loopback1
B        192.51.100.10/31 [200/0] via 192.51.100.1, 00:02:16

```




What is happening on wire CSR4 to CSR2 (vlan 24)?


```
CSR4#ping vrf AS64499 192.0.2.10 source Lo1 df-bit size 1500
Type escape sequence to abort.
Sending 5, 1500-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.4 
Packet sent with the DF bit set
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/3 ms

```

tcpdump 
Note length is 1526Bytes for outgoing frame/packet

14 (Ethernet) + 4 (VLAN) + 4 (MPLS label) +  4 (VPNv4 label) + 20 (IP) +  1480 (ICMP)

Ethernet and VLAN considered Layer2

MPLS/VPNv4/IP consider Layer3 and all contribute to ```mtu```.  ```mtu``` normally set to 1500 but many services use 
additional headers for encapsulation the ```mtu``` has been adjusted as per: [proxmox-mtu](https://github.com/inband/spcor/blob/master/3-mpls-and-segment-routing/1-implement-mpls/proxmox-mtu.md)


```
root@pve6-lab:~# tcpdump -nntei vmbr0v24 'mpls'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on vmbr0v24, link-type EN10MB (Ethernet), capture size 262144 bytes
f6:5a:cd:d6:fc:26 > 3a:28:c6:0b:96:7d, ethertype 802.1Q (0x8100), length 1526: vlan 24, p 0, ethertype MPLS unicast, MPLS (label 18, exp 0, ttl 255) (label 21, exp 0, [S], ttl 255) 192.51.100.4 > 192.0.2.10: ICMP echo request, id 4, seq 0, length 1480
3a:28:c6:0b:96:7d > f6:5a:cd:d6:fc:26, ethertype 802.1Q (0x8100), length 1522: vlan 24, p 0, ethertype MPLS unicast, MPLS (label 19, exp 0, [S], ttl 253) 192.0.2.10 > 192.51.100.4: ICMP echo reply, id 4, seq 0, length 1480

```
