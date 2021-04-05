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
