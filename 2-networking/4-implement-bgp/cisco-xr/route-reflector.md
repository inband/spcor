# Route Reflector

```
RP/0/RP0/CPU0:XR2#show run router bgp 
Mon Apr  5 07:38:35.665 UTC
router bgp 123
 bgp router-id 12.12.12.12
 address-family vpnv4 unicast
 !
 neighbor 1.1.1.1
  remote-as 123
  update-source Loopback0
  address-family vpnv4 unicast
   route-reflector-client
  !
 !
 neighbor 3.3.3.3
  remote-as 123
  update-source Loopback0
  address-family vpnv4 unicast
   route-reflector-client
  !
 !
 neighbor 11.11.11.11
  remote-as 123
  update-source Loopback0
  address-family vpnv4 unicast
   route-reflector-client
  !
 !
 neighbor 13.13.13.13
  remote-as 123
  update-source Loopback0
  address-family vpnv4 unicast
   route-reflector-client
  !
 !
!
          

RP/0/RP0/CPU0:XR2#show bgp vpnv4 unicast summary 
Mon Apr  5 07:38:56.100 UTC
BGP router identifier 12.12.12.12, local AS number 123
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
1.1.1.1           0   123    3181    2889        3    0    0    2d00h          1
3.3.3.3           0   123    3174    2887        3    0    0    2d00h          1
11.11.11.11       0   123    2882    2884        3    0    0    2d00h          0
13.13.13.13       0   123    2881    2883        3    0    0    1d23h          0

```


How do you verify a route reflector is working?

Well we know that route reflector design is chosen to avoid full iBGP mesh. So pick a route-reflector client and look at BGP routes.

```
R3#show bgp vpnv4 unicast all 7.7.7.1
BGP routing table entry for 7:7:7.7.7.1/32, version 3
Paths: (2 available, best #2, table X)
  Not advertised to any peer
  Refresh Epoch 1
  Local
    1.1.1.1 (metric 3) (via default) from 12.12.12.12 (12.12.12.12)
      Origin IGP, metric 0, localpref 100, valid, internal
      Extended Community: RT:7:7
      Originator: 1.1.1.1, Cluster list: 12.12.12.12
      mpls labels in/out nolabel/35
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 2
  Local
    1.1.1.1 (metric 3) (via default) from 2.2.2.2 (2.2.2.2)
      Origin IGP, metric 0, localpref 100, valid, internal, best
      Extended Community: RT:7:7
      Originator: 1.1.1.1, Cluster list: 2.2.2.2
      mpls labels in/out nolabel/35
      rx pathid: 0, tx pathid: 0x0

```


What would be minimum requirement to verify rr?

2 - client and "server"

```
RP/0/RP0/CPU0:XR2#show bgp vpnv4 unicast nei 1.1.1.1                         
Mon Apr  5 08:13:28.066 UTC

BGP neighbor is 1.1.1.1
 Remote AS 123, local AS 123, internal link
 Remote router ID 1.1.1.1
 Cluster ID 12.12.12.12
  BGP state = Established, up for 00:24:48
  NSR State: None
  Last read 00:00:38, Last read before reset 00:25:35
  Hold time is 180, keepalive interval is 60 seconds
  Configured hold time: 180, keepalive: 60, min acceptable hold time: 3
  Last write 00:00:43, attempted 19, written 19
  Second last write 00:01:43, attempted 19, written 19
  Last write before reset 00:25:30, attempted 241, written 241
  Second last write before reset 00:25:35, attempted 94, written 94
  Last write pulse rcvd  Apr  5 08:12:53.262 last full not set pulse count 6139
  Last write pulse rcvd before reset 00:25:30
  Socket not armed for io, armed for read, armed for write
  Last write thread event before reset 00:25:11, second last 00:25:30
  Last KA expiry before reset 00:00:00, second last 00:00:00
  Last KA error before reset 00:00:00, KA not sent 00:00:00
  Last KA start before reset 00:25:30, second last 00:00:00
  Precedence: internet
  Non-stop routing is enabled
  Multi-protocol capability received
  Neighbor capabilities:
    Route refresh: advertised (old + new) and received (old + new)
    4-byte AS: advertised and received
    Address family VPNv4 Unicast: advertised and received
  Received 3224 messages, 0 notifications, 0 in queue
  Sent 2932 messages, 0 notifications, 0 in queue
  Minimum time between advertisement runs is 0 secs
  Inbound message logging enabled, 3 messages buffered
  Outbound message logging enabled, 3 messages buffered
          
 For Address Family: VPNv4 Unicast
  BGP neighbor version 7
  Update group: 0.2 Filter-group: 0.1  No Refresh request being processed
  Route-Reflector Client                              ####################  RR Client
    Extended Nexthop Encoding: advertised
  Route refresh request: received 1, sent 0
  1 accepted prefixes, 1 are bestpaths
  Exact no. of prefixes denied : 0.
  Cumulative no. of prefixes denied: 0. 
  Prefix advertised 2, suppressed 0, withdrawn 0
  Maximum prefixes allowed 2097152
  Threshold for warning message 75%, restart interval 0 min
  AIGP is enabled
  An EoR was not received during read-only mode
  Last ack version 7, Last synced ack version 0
  Outstanding version objects: current 0, max 2, refresh 0
  Additional-paths operation: None
  Send Multicast Attributes

  Connections established 3; dropped 2
  Local host: 12.12.12.12, Local port: 179, IF Handle: 0x00000000
  Foreign host: 1.1.1.1, Foreign port: 41817
  Last reset 00:24:59, due to RR client configuration changed
```

or 

Route-relector is reflecting iBGP route back to client.  This is not-allowed in normal iBGP operation.

```
RP/0/RP0/CPU0:XR2#show bgp vpnv4 unicast nei 1.1.1.1 advertised-routes 
Mon Apr  5 08:15:16.803 UTC
Network            Next Hop        From            AS Path
Route Distinguisher: 7:7
7.7.7.1/32         12.12.12.12     1.1.1.1         i                ####### THIS
7.7.7.3/32         3.3.3.3         3.3.3.3         ?

Processed 2 prefixes, 2 paths
```




