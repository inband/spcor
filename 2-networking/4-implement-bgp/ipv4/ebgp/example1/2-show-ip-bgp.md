# prefix-lists


Now eBGP session is UP between CSR10 and CSR1

View summary and routes, advertised-routes and neighbor information (CSR10) 

```
CSR10#show ip bgp summary 
BGP router identifier 192.0.2.10, local AS number 64496
BGP table version is 8, main routing table version 8
7 network entries using 1736 bytes of memory
7 path entries using 840 bytes of memory
3/3 BGP path/bestpath attribute entries using 744 bytes of memory
1 BGP AS-PATH entries using 24 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 3344 total bytes of memory
BGP activity 9/2 prefixes, 9/2 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
192.51.100.11   4        64499      34      32        8    0    0 00:25:04        6
```


```
CSR10#show ip bgp neighbors 192.51.100.11 routes 
BGP table version is 8, local router ID is 192.0.2.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  192.51.100.0     192.51.100.11            0             0 64499 i
 *>  192.51.100.1/32  192.51.100.11            0             0 64499 ?
 *>  192.51.100.5/32  192.51.100.11            0             0 64499 ?
 r>  192.51.100.10/31 192.51.100.11            0             0 64499 ?
 *>  192.51.100.200/31
                       192.51.100.11            0             0 64499 ?
 *>  192.51.100.202/31
                       192.51.100.11            0             0 64499 ?

Total number of prefixes 6 
```

```
CSR10#show ip bgp neighbors 192.51.100.11 advertised-routes 
BGP table version is 8, local router ID is 192.0.2.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  192.0.2.0        0.0.0.0                  0         32768 i

Total number of prefixes 1 
```

```
CSR10#show ip bgp neighbors 192.51.100.11                   
BGP neighbor is 192.51.100.11,  remote AS 64499, external link
  BGP version 4, remote router ID 192.51.100.1
  BGP state = Established, up for 00:29:18
  Last read 00:00:41, last write 00:00:12, hold time is 180, keepalive interval is 60 seconds
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
    Updates:                2          3
    Keepalives:            34         34
    Route Refresh:          0          0
    Total:                 37         38
  Default minimum time between advertisement runs is 30 seconds

 For address family: IPv4 Unicast
  Session: 192.51.100.11
  BGP table version 8, neighbor version 8/0
  Output queue size : 0
  Index 1, Advertise bit 0
  1 update-group member
  Slow-peer detection is disabled
  Slow-peer split-update-group dynamic is disabled
  Interface associated: GigabitEthernet2.10
                                 Sent       Rcvd
  Prefix activity:               ----       ----
    Prefixes Current:               1          6 (Consumes 720 bytes)
    Prefixes Total:                 1          6
    Implicit Withdraw:              0          0
    Explicit Withdraw:              0          0
    Used as bestpath:             n/a          6
    Used as multipath:            n/a          0

                                   Outbound    Inbound
  Local Policy Denied Prefixes:    --------    -------
    Bestpath from this peer:              6        n/a
    Total:                                6          0
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

  Address tracking is enabled, the RIB does have a route to 192.51.100.11
  Connections established 1; dropped 0
  Last reset never
  Transport(tcp) path-mtu-discovery is enabled
  Graceful-Restart is disabled
Connection state is ESTAB, I/O status: 1, unread input bytes: 0            
Connection is ECN Disabled, Mininum incoming TTL 0, Outgoing TTL 1
Local host: 192.51.100.10, Local port: 22138
Foreign host: 192.51.100.11, Foreign port: 179
Connection tableid (VRF): 0
Maximum output segment queue size: 50

Enqueued packets for retransmit: 0, input: 0  mis-ordered: 0 (0 bytes)

Event Timers (current time is 0xAF21688):
Timer          Starts    Wakeups            Next
Retrans            36          0             0x0
TimeWait            0          0             0x0
AckHold            35         32             0x0
SendWnd             0          0             0x0
KeepAlive           0          0             0x0
GiveUp              0          0             0x0
PmtuAger          905        904       0xAF217E8
DeadWait            0          0             0x0
Linger              0          0             0x0
ProcessQ            0          0             0x0

iss: 3612284666  snduna: 3612285447  sndnxt: 3612285447
irs:  414735957  rcvnxt:  414736813

sndwnd:  15604  scale:      0  maxrcvwnd:  16384
rcvwnd:  15529  scale:      0  delrcvwnd:    855

SRTT: 992 ms, RTTO: 1059 ms, RTV: 67 ms, KRTT: 0 ms
minRTT: 2 ms, maxRTT: 1000 ms, ACK hold: 200 ms
uptime: 1758960 ms, Sent idletime: 12953 ms, Receive idletime: 12751 ms 
Status Flags: active open
Option Flags: nagle, path mtu capable
IP Precedence value : 6

Datagrams (max data segment is 1460 bytes):
Rcvd: 71 (out of order: 0), with data: 36, total data bytes: 855
Sent: 72 (retransmit: 0, fastretransmit: 0, partialack: 0, Second Congestion: 0), with data: 36, total data bytes: 780
          
 Packets received in fast path: 0, fast processed: 0, slow path: 0
 fast lock acquisition failures: 0, slow path: 0
TCP Semaphore      0x7FEF17B9D050  FREE 
```


View summary and eBGP routes (CSR1)

```
CSR1#show ip bgp summary 
BGP router identifier 192.51.100.1, local AS number 64499
BGP table version is 8, main routing table version 8
7 network entries using 1736 bytes of memory
7 path entries using 840 bytes of memory
3/3 BGP path/bestpath attribute entries using 744 bytes of memory
1 BGP AS-PATH entries using 24 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 3344 total bytes of memory
BGP activity 7/0 prefixes, 8/1 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
192.51.100.2    4        64499    5922    5928        8    0    0 3d17h           0
192.51.100.10   4        64496      34      36        8    0    0 00:27:02        1
```

```
CSR1#show ip bgp neighbors 192.51.100.10 routes 
BGP table version is 8, local router ID is 192.51.100.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  192.0.2.0        192.51.100.10            0             0 64496 i

Total number of prefixes 1 
```
