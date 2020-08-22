# configure L3VPN for AS64499 on CSR1


Create new vrf AS64499 - this vrf will be used for INTERNET routes and will connect to peers from other ASs.

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
```

Remove existing eBGP peer to CSR10

```
CSR1(config)#router bgp 64499
CSR1(config-router)#no  neighbor 192.51.100.10 remote-as 64496
```

Configure router id

```
CSR1(config)#router bgp 64499                          
CSR1(config-router)#bgp router-id 192.51.100.1 
```

Configure a loopback - Lo1 ```192.51.100.1``` 

```
interface Loopback1
 vrf forwarding AS64499
 ip address 192.51.100.1 255.255.255.255
end
```

Place interface facing AS64496 in ```vrf AS64496```

```
CSR1(config)#do show run int Gi4.10
Building configuration...

Current configuration : 103 bytes
!
interface GigabitEthernet4.10
 encapsulation dot1Q 10
 ip address 192.51.100.11 255.255.255.254
end

CSR1(config)#interface GigabitEthernet4.10
CSR1(config-subif)#vrf forwarding AS64499
% Interface GigabitEthernet4.10 IPv4 disabled and address(es) removed due to disabling VRF AS64499
CSR1(config-subif)# ip address 192.51.100.11 255.255.255.254
% Warning: use /31 mask on non point-to-point interface cautiously
```

Check route in ```vrf AS64499```

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

      192.51.100.0/24 is variably subnetted, 3 subnets, 2 masks
C        192.51.100.1/32 is directly connected, Loopback1
C        192.51.100.10/31 is directly connected, GigabitEthernet4.10
L        192.51.100.11/32 is directly connected, GigabitEthernet4.10
```

Can IP  ```192.51.100.10``` be reached?  That is other end of link AS64496

```
CSR1#ping vrf AS64499 192.51.100.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.51.100.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
```

Yes - but what about sourcing Lo1.  This will fail as eBGP needs to be setup.

```
CSR1#ping vrf AS64499 192.51.100.10 source Lo1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.51.100.10, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
.....
Success rate is 0 percent (0/5)

```

Configure eBGP peer 64496 under ```vrf 64499```

```
 address-family vpnv4
 exit-address-family
 !
 address-family ipv4 vrf AS64499
  neighbor 192.51.100.10 remote-as 64496
  neighbor 192.51.100.10 update-source GigabitEthernet4.10
  neighbor 192.51.100.10 activate
  neighbor 192.51.100.10 prefix-list PL_EBGP_OUT out
 exit-address-family

```

CSR1 is now receiving eBGP prefix from CSR10

```
CSR1#show bgp vpnv4 unicast vrf AS64499 summary 
BGP router identifier 192.51.100.1, local AS number 64499
BGP table version is 2, main routing table version 2
1 network entries using 256 bytes of memory
1 path entries using 120 bytes of memory
2/1 BGP path/bestpath attribute entries using 528 bytes of memory
1 BGP AS-PATH entries using 24 bytes of memory
1 BGP extended community entries using 24 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 952 total bytes of memory
BGP activity 16/14 prefixes, 17/15 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
192.51.100.10   4        64496      13      12        2    0    0 00:07:24        1

```

CSR1 not advertising routes to CSR10

```
CSR1#show bgp vpnv4 unicast vrf AS64499 neighbors 192.51.100.10 advertised-routes 

Total number of prefixes 0 
```

Add routes to vrf ```AS64499```

```
CSR1#conf t                                                                       
Enter configuration commands, one per line.  End with CNTL/Z.
CSR1(config)#router bgp 64499             
CSR1(config-router)# address-family ipv4 vrf AS64499                        
CSR1(config-router-af)#net
CSR1(config-router-af)#network 192.51.100.0

CSR1(config)#ip route vrf AS64499 192.51.100.0 255.255.255.0 null 0

```

Full BGP config

```
router bgp 64499
 bgp router-id 192.51.100.1
 bgp log-neighbor-changes
 network 192.51.100.0
 !
 address-family vpnv4
 exit-address-family
 !
 address-family ipv4 vrf AS64499
  network 192.51.100.0
  neighbor 192.51.100.10 remote-as 64496
  neighbor 192.51.100.10 update-source GigabitEthernet4.10
  neighbor 192.51.100.10 activate
  neighbor 192.51.100.10 prefix-list PL_EBGP_OUT out
 exit-address-family
!
!
ip route vrf AS64499 192.51.100.0 255.255.255.0 Null0
!
ip prefix-list PL_EBGP_OUT seq 10 permit 192.51.100.0/24
ip prefix-list PL_EBGP_OUT seq 1000 deny 0.0.0.0/0 le 32
!


```




Check again

```
CSR1#show bgp vpnv4 unicast vrf AS64499 neighbors 192.51.100.10 advertised-routes 
BGP table version is 3, local router ID is 192.51.100.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
Route Distinguisher: 64499:1 (default for vrf AS64499)
 *>  192.51.100.0     0.0.0.0                  0         32768 i

Total number of prefixes 1 
```

Verify reachability
```
CSR1#ping vrf AS64499 192.51.100.10 source lo1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.51.100.10, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms


CSR1#ping vrf AS64499 192.0.2.10 source lo1   
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms

```

### show bgp vpnv4 neighbor

The interesting part is 

```
 For address family: VPNv4 Unicast
  Translates address family IPv4 Unicast for VRF AS64499
```



Full output

```
CSR1#show bgp vpnv4 unicast vrf AS64499 neighbors 192.51.100.10
BGP neighbor is 192.51.100.10,  vrf AS64499,  remote AS 64496, external link
  BGP version 4, remote router ID 192.0.2.10
  BGP state = Established, up for 00:13:30
  Last read 00:00:48, last write 00:00:28, hold time is 180, keepalive interval is 60 seconds
  Neighbor sessions:
    1 active, is not multisession capable (disabled)
  Neighbor capabilities:
    Route refresh: advertised and received(new)
    Four-octets ASN Capability: advertised and received
    Address family IPv4 Unicast: advertised and received
    Enhanced Refresh Capability: advertised and received
    Multisession Capability: 
    Stateful switchover support enabled: NO for session 1
  Message statistics:
    InQ depth is 0
    OutQ depth is 0
    
                         Sent       Rcvd
    Opens:                  1          1
    Notifications:          0          0
    Updates:                3          2
    Keepalives:            15         16
    Route Refresh:          0          0
    Total:                 19         19
  Default minimum time between advertisement runs is 0 seconds

 For address family: VPNv4 Unicast
  Translates address family IPv4 Unicast for VRF AS64499
  Session: 192.51.100.10
  BGP table version 3, neighbor version 3/0
  Output queue size : 0
  Index 2, Advertise bit 0
  2 update-group member
  Outgoing update prefix filter list is PL_EBGP_OUT
  Slow-peer detection is disabled
  Slow-peer split-update-group dynamic is disabled
  Interface associated: GigabitEthernet4.10
                                 Sent       Rcvd
  Prefix activity:               ----       ----
    Prefixes Current:               1          1 (Consumes 120 bytes)
    Prefixes Total:                 1          1
    Implicit Withdraw:              0          0
    Explicit Withdraw:              1          0
    Used as bestpath:             n/a          1
    Used as multipath:            n/a          0

                                   Outbound    Inbound
  Local Policy Denied Prefixes:    --------    -------
    Bestpath from this peer:              1        n/a
    Total:                                1          0
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

  Address tracking is enabled, the RIB does have a route to 192.51.100.10
  Connections established 1; dropped 0
  Last reset never
  Transport(tcp) path-mtu-discovery is enabled
  Graceful-Restart is disabled
Connection state is ESTAB, I/O status: 1, unread input bytes: 0            
Connection is ECN Disabled, Mininum incoming TTL 0, Outgoing TTL 1
Local host: 192.51.100.11, Local port: 179
Foreign host: 192.51.100.10, Foreign port: 26204
Connection tableid (VRF): 2
Maximum output segment queue size: 50

Enqueued packets for retransmit: 0, input: 0  mis-ordered: 0 (0 bytes)

Event Timers (current time is 0x53C851F):
Timer          Starts    Wakeups            Next
Retrans            18          0             0x0
TimeWait            0          0             0x0
AckHold            17         14             0x0
SendWnd             0          0             0x0
KeepAlive           0          0             0x0
GiveUp              0          0             0x0
PmtuAger            0          0             0x0
DeadWait            0          0             0x0
Linger              0          0             0x0
ProcessQ            0          0             0x0

iss: 2436381953  snduna: 2436382400  sndnxt: 2436382400
irs: 3666703047  rcvnxt: 3666703486

sndwnd:  15938  scale:      0  maxrcvwnd:  16384
rcvwnd:  15946  scale:      0  delrcvwnd:    438

SRTT: 909 ms, RTTO: 1600 ms, RTV: 691 ms, KRTT: 0 ms
minRTT: 0 ms, maxRTT: 1000 ms, ACK hold: 200 ms
uptime: 810540 ms, Sent idletime: 28256 ms, Receive idletime: 28053 ms 
Status Flags: passive open, gen tcbs
Option Flags: VRF id set, nagle, path mtu capable
IP Precedence value : 6
          
Datagrams (max data segment is 1460 bytes):
Rcvd: 37 (out of order: 0), with data: 18, total data bytes: 438
Sent: 36 (retransmit: 0, fastretransmit: 0, partialack: 0, Second Congestion: 0), with data: 19, total data bytes: 446

 Packets received in fast path: 0, fast processed: 0, slow path: 0
 fast lock acquisition failures: 0, slow path: 0
TCP Semaphore      0x7FFA8D4C1388  FREE 
CSR1# 


```

