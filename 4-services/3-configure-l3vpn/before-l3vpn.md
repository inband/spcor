# CSR1 before L3VPN

CSR1 is part of AS64499

CSR10 peers with AS64496

Relevant configuration

```
interface GigabitEthernet4.10
 encapsulation dot1Q 10
 ip address 192.51.100.11 255.255.255.254
!
router bgp 64499
 bgp log-neighbor-changes
 network 192.51.100.0
 neighbor 192.51.100.10 remote-as 64496
 neighbor 192.51.100.10 update-source GigabitEthernet4.10
 neighbor 192.51.100.10 prefix-list PL_EBGP_OUT out
!
ip route 192.51.100.0 255.255.255.0 Null0
ip route vrf LAB_MGMT 192.168.199.3 255.255.255.255 192.168.250.1
!
!
ip prefix-list PL_EBGP_OUT seq 10 permit 192.51.100.0/24
ip prefix-list PL_EBGP_OUT seq 1000 deny 0.0.0.0/0 le 32
!
```


eBGP is **ip bgp**

```
CSR1#show ip bgp summary 
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
192.51.100.10   4        64496     424     422        9    0    0 06:17:09        1

```

Note Address family IPv4 Unicast

```
CSR1#show ip bgp neighbors 192.51.100.10 
BGP neighbor is 192.51.100.10,  remote AS 64496, external link
  BGP version 4, remote router ID 192.0.2.10
  BGP state = Established, up for 06:18:26
  Last read 00:00:41, last write 00:00:25, hold time is 180, keepalive interval is 60 seconds
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
    Updates:                2          6
    Keepalives:           420        418
    Route Refresh:          0          0
    Total:                423        425
  Default minimum time between advertisement runs is 30 seconds

 For address family: IPv4 Unicast
  Session: 192.51.100.10
  BGP table version 9, neighbor version 9/0
  Output queue size : 0
  Index 5, Advertise bit 0
  5 update-group member
  Outgoing update prefix filter list is PL_EBGP_OUT
  Slow-peer detection is disabled
  Slow-peer split-update-group dynamic is disabled
  Interface associated: GigabitEthernet4.10
                                 Sent       Rcvd
  Prefix activity:               ----       ----
    Prefixes Current:               1          1 (Consumes 120 bytes)
    Prefixes Total:                 1          5
    Implicit Withdraw:              0          1
    Explicit Withdraw:              0          3
    Used as bestpath:             n/a          1
    Used as multipath:            n/a          0

                                   Outbound    Inbound
  Local Policy Denied Prefixes:    --------    -------
    Bestpath from this peer:              4        n/a
    Total:                                4          0
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
  Connections established 2; dropped 1
  Last reset 06:18:26, due to User reset of session 1
  Transport(tcp) path-mtu-discovery is enabled
  Graceful-Restart is disabled
Connection state is ESTAB, I/O status: 1, unread input bytes: 0            
Connection is ECN Disabled, Mininum incoming TTL 0, Outgoing TTL 1
Local host: 192.51.100.11, Local port: 13592
Foreign host: 192.51.100.10, Foreign port: 179
Connection tableid (VRF): 0
Maximum output segment queue size: 50

Enqueued packets for retransmit: 0, input: 0  mis-ordered: 0 (0 bytes)

Event Timers (current time is 0x51C5E02):
Timer          Starts    Wakeups            Next
Retrans           423          0             0x0
TimeWait            0          0             0x0
AckHold           420        408             0x0
SendWnd             0          0             0x0
KeepAlive           0          0             0x0
GiveUp              0          0             0x0
PmtuAger        21770      21769       0x51C5EF0
DeadWait            0          0             0x0
Linger              0          0             0x0
ProcessQ            0          0             0x0

iss:   68040203  snduna:   68048318  sndnxt:   68048318
irs: 2695420226  rcvnxt: 2695428487

sndwnd:  15586  scale:      0  maxrcvwnd:  16384
rcvwnd:  15453  scale:      0  delrcvwnd:    931

SRTT: 1000 ms, RTTO: 1003 ms, RTV: 3 ms, KRTT: 0 ms
minRTT: 1 ms, maxRTT: 1000 ms, ACK hold: 200 ms
uptime: 22706664 ms, Sent idletime: 25715 ms, Receive idletime: 25513 ms 
Status Flags: active open
Option Flags: nagle, path mtu capable
IP Precedence value : 6

Datagrams (max data segment is 1460 bytes):
Rcvd: 843 (out of order: 0), with data: 421, total data bytes: 8260
Sent: 840 (retransmit: 0, fastretransmit: 0, partialack: 0, Second Congestion: 0), with data: 423, total data bytes: 8114

 Packets received in fast path: 0, fast processed: 0, slow path: 0
 fast lock acquisition failures: 0, slow path: 0
TCP Semaphore      0x7FFA8D4C12C8  FREE 

```
