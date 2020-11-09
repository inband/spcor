# BGP configuration

```PE1```

```
set logical-systems PE1 protocols bgp group iBGP-RR type internal
set logical-systems PE1 protocols bgp group iBGP-RR local-address 172.16.0.11
set logical-systems PE1 protocols bgp group iBGP-RR family inet unicast add-path receive
set logical-systems PE1 protocols bgp group iBGP-RR family inet unicast add-path send path-count 6
set logical-systems PE1 protocols bgp group iBGP-RR export PL-iBGP-RR-OUT
set logical-systems PE1 protocols bgp group iBGP-RR neighbor 172.16.0.201
set logical-systems PE1 protocols bgp group iBGP-RR neighbor 172.16.0.202
set logical-systems PE1 policy-options policy-statement PL-iBGP-RR-OUT term NHS from family inet
set logical-systems PE1 policy-options policy-statement PL-iBGP-RR-OUT term NHS then next-hop self
set logical-systems PE1 routing-options autonomous-system 65000
```

```
root@VMX1:PE1> show bgp summary         
Groups: 1 Peers: 2 Down peers: 2
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0               
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
172.16.0.201          65000          0          0       0       0       56:11 Active
172.16.0.202          65000          0          0       0       0       56:11 Active
```

--------------------------------------

```PE3```

```
set logical-systems PE3 protocols bgp group iBGP-RR type internal
set logical-systems PE3 protocols bgp group iBGP-RR local-address 172.16.0.33
set logical-systems PE3 protocols bgp group iBGP-RR family inet unicast add-path receive
set logical-systems PE3 protocols bgp group iBGP-RR family inet unicast add-path send path-count 6
set logical-systems PE3 protocols bgp group iBGP-RR export PL-iBGP-RR-OUT
set logical-systems PE3 protocols bgp group iBGP-RR neighbor 172.16.0.201
set logical-systems PE3 protocols bgp group iBGP-RR neighbor 172.16.0.202
set logical-systems PE3 policy-options policy-statement PL-iBGP-RR-OUT term NHS from family inet
set logical-systems PE3 policy-options policy-statement PL-iBGP-RR-OUT term NHS then next-hop self
set logical-systems PE3 routing-options autonomous-system 65000
```
---------------------------------
```RR1```

Note - not creating NHS for RR1

```
set logical-systems RR1 protocols bgp group iBGP-CLIENTS type internal
set logical-systems RR1 protocols bgp group iBGP-CLIENTS local-address 172.16.0.201
set logical-systems RR1 protocols bgp group iBGP-CLIENTS family inet unicast add-path receive
set logical-systems RR1 protocols bgp group iBGP-CLIENTS family inet unicast add-path send path-count 6
set logical-systems RR1 protocols bgp group iBGP-CLIENTS cluster 172.16.0.201
set logical-systems RR1 protocols bgp group iBGP-CLIENTS neighbor 172.16.0.11
set logical-systems RR1 protocols bgp group iBGP-CLIENTS neighbor 172.16.0.22
set logical-systems RR1 protocols bgp group iBGP-CLIENTS neighbor 172.16.0.33
set logical-systems RR1 protocols bgp group iBGP-CLIENTS neighbor 172.16.0.44
set logical-systems RR1 protocols bgp group iBGP-RR type internal
set logical-systems RR1 protocols bgp group iBGP-RR local-address 172.16.0.201
set logical-systems RR1 protocols bgp group iBGP-RR family inet unicast add-path receive
set logical-systems RR1 protocols bgp group iBGP-RR family inet unicast add-path send path-count 6
set logical-systems RR1 protocols bgp group iBGP-RR neighbor 172.16.0.202
set logical-systems RR1 protocols isis overload
set logical-systems RR1 protocols isis level 2 wide-metrics-only
set logical-systems RR1 protocols isis interface lt-0/0/0.42
set logical-systems RR1 protocols isis interface ge-0/0/4.47
set logical-systems RR1 protocols isis interface ge-0/0/5.45
set logical-systems RR1 protocols isis interface lo0.4
set logical-systems RR1 routing-options autonomous-system 65000
```


```
root@VMX1:RR1# run show bgp summary    
Groups: 2 Peers: 5 Down peers: 3
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0               
                       0          0          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
172.16.0.11           65000         11         11       0       0        4:18 0/0/0/0              0/0/0/0
172.16.0.22           65000          0          0       0       0        4:32 Active
172.16.0.33           65000         11         11       0       0        4:11 0/0/0/0              0/0/0/0
172.16.0.44           65000          0          0       0       0        4:32 Active
172.16.0.202          65000          0          0       0       0        1:26 Active
```


----------------------------------






------------------------------------

```PE4```

```
hostname PE4
!
route-policy PL-iBGP-RR-OUT
  set next-hop self
end-policy
!
router bgp 65000
 address-family ipv4 unicast
  additional-paths receive
  additional-paths send
 !
 neighbor-group RR
  remote-as 65000
  update-source Loopback0
  address-family ipv4 unicast
   route-policy PL-iBGP-RR-OUT out
  !
 !
 neighbor 172.16.0.201
  use neighbor-group RR
 !
 neighbor 172.16.0.202
  use neighbor-group RR
 !
!

```

```
RP/0/RP0/CPU0:PE4#show bgp summary 
Mon Nov  9 00:27:21.147 UTC
BGP router identifier 172.16.0.44, local AS number 65000
BGP generic scan interval 60 secs
Non-stop routing is enabled
BGP table state: Active
Table ID: 0xe0000000   RD version: 2
BGP main routing table version 2
BGP NSR Initial initsync version 4294967295 (Not Reached)
BGP NSR/ISSU Sync-Group versions 0/0
BGP scan interval 60 secs

BGP is operating in STANDALONE mode.


Process       RcvTblVer   bRIB/RIB   LabelVer  ImportVer  SendTblVer  StandbyVer
Speaker               2          1          2          0           1           0

Neighbor        Spk    AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down  St/PfxRcd
172.16.0.201      0 65000       2       2        0    0    0 00:00:01          0
172.16.0.202      0 65000       0       0        0    0    0 00:00:00 Active
```
