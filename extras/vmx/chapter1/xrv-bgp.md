# XRv BGP


```
RP/0/RP0/CPU0:PE2#show bgp summary 
Thu Nov 12 03:45:16.541 UTC
BGP router identifier 172.16.0.22, local AS number 65000
BGP generic scan interval 60 secs
Non-stop routing is enabled
BGP table state: Active
Table ID: 0xe0000000   RD version: 82
BGP main routing table version 82
BGP NSR Initial initsync version 2 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0
BGP scan interval 60 secs

BGP is operating in STANDALONE mode.


Process       RcvTblVer   bRIB/RIB   LabelVer  ImportVer  SendTblVer  StandbyVer
Speaker              82         82         82         82          82           0

Neighbor        Spk    AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down  St/PfxRcd
10.1.0.2          0 65001    9604    8630       82    0    0    2d22h          2
10.1.0.4          0 65001    9598    8630       82    0    0    2d22h          0
172.16.0.201      0 65000    9830    8968       82    0    0    3d02h          6
172.16.0.202      0 65000    4495    4496       82    0    0    3d02h          4
```



```
RP/0/RP0/CPU0:PE2#show bgp ipv4 unicast neighbor 172.16.0.201 advertised-routes
Thu Nov 12 03:46:55.263 UTC
Network            Next Hop        From            AS Path
10.1.0.2/31        172.16.0.22     Local           ?
10.1.0.4/31        172.16.0.22     Local           ?
192.168.10.1/32    172.16.0.22     10.1.0.2        65001i
192.168.10.2/32    172.16.0.22     10.1.0.2        65001i

Processed 4 prefixes, 4 paths
```

```
RP/0/RP0/CPU0:PE2#show bgp ipv4 unicast neighbor 172.16.0.201 routes         
Thu Nov 12 03:47:09.351 UTC
BGP router identifier 172.16.0.22, local AS number 65000
BGP generic scan interval 60 secs
Non-stop routing is enabled
BGP table state: Active
Table ID: 0xe0000000   RD version: 82
BGP main routing table version 82
BGP NSR Initial initsync version 2 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0
BGP scan interval 60 secs

Status codes: s suppressed, d damped, h history, * valid, > best
              i - internal, r RIB-failure, S stale, N Nexthop-discard
Origin codes: i - IGP, e - EGP, ? - incomplete
   Network            Next Hop            Metric LocPrf Weight Path
* i192.168.10.1/32    172.16.0.11              1    100      0 65001 i
* i192.168.10.2/32    172.16.0.11                   100      0 65001 i
* i192.168.20.3/32    172.16.0.44                   100      0 65002 i
*>i                   172.16.0.33              0    100      0 65002 i
* i192.168.20.4/32    172.16.0.44              0    100      0 65002 i
*>i                   172.16.0.33                   100      0 65002 i

Processed 4 prefixes, 6 paths
```
