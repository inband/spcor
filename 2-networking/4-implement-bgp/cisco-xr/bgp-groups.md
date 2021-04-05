# BGP session-group and af-group

Peer ```1.1.1.1``` is using groups (session-group and af-group)

```
RP/0/RP0/CPU0:XR2#show run router bgp            
Mon Apr  5 07:49:13.813 UTC
router bgp 123
 bgp router-id 12.12.12.12
 address-family vpnv4 unicast
 !
 af-group VPNv4 address-family vpnv4 unicast
  route-reflector-client
 !
 session-group IBGP_PEERS
  remote-as 123
  update-source Loopback0
 !
 neighbor 1.1.1.1
  use session-group IBGP_PEERS
  address-family vpnv4 unicast
   use af-group VPNv4
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


```

Verify session is up.

```
RP/0/RP0/CPU0:XR2#show bgp vpnv4 unicast summary 
Mon Apr  5 07:48:48.593 UTC
BGP router identifier 12.12.12.12, local AS number 123
BGP generic scan interval 60 secs
Non-stop routing is enabled
BGP table state: Active
Table ID: 0x0   RD version: 0
BGP main routing table version 7
BGP NSR Initial initsync version 1 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0
BGP scan interval 60 secs

BGP is operating in STANDALONE mode.


Process       RcvTblVer   bRIB/RIB   LabelVer  ImportVer  SendTblVer  StandbyVer
Speaker               7          7          7          7           7           0

Neighbor        Spk    AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down  St/PfxRcd
1.1.1.1           0   123    3198    2908        7    0    0 00:00:07          1
3.3.3.3           0   123    3185    2900        7    0    0    2d00h          1
11.11.11.11       0   123    2892    2897        7    0    0    2d00h          0
13.13.13.13       0   123    2891    2896        7    0    0    2d00h          0
```



