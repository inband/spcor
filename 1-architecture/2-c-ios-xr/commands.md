# commands


commit 
IOS:wr mem, copy run start

```
RP/0/RP0/CPU0:ios(config)#hostname XRV1
RP/0/RP0/CPU0:ios(config)#commit
Sat Aug 29 09:10:45.735 UTC
RP/0/RP0/CPU0:XRV1(config)#
```

vrf

```
vrf LAB_MGMT
 rd 64507:999
 address-family ipv4 unicast
  import route-target
   64507:999
  !
  export route-target
   64507:999
  !
 !
!
```
