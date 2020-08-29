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

show route ipv4
IOS: show ip route, show ip route vrf LAB_MGMT

```
RP/0/RP0/CPU0:XRV1#show route ipv4 


RP/0/RP0/CPU0:XRV1#show route vrf LAB_MGMT ipv4
```

Add interface to vrf

```
RP/0/RP0/CPU0:XRV1(config)#interface MgmtEth0/RP0/CPU0/0
RP/0/RP0/CPU0:XRV1(config-if)#vrf ? 
  WORD  VRF name
RP/0/RP0/CPU0:XRV1(config-if)#vrf LAB_MGMT ?
  <cr>  
RP/0/RP0/CPU0:XRV1(config-if)#vrf LAB_MGMT 
RP/0/RP0/CPU0:XRV1(config-if)#commit       
Sat Aug 29 11:00:38.727 UTC

% Failed to commit one or more configuration items during a pseudo-atomic operation. All changes made have been reverted. Please issue 'show configuration failed [inheritance]' from this session to view the errors
RP/0/RP0/CPU0:XRV1(config-if)#show configuration f
failed  formal  
RP/0/RP0/CPU0:XRV1(config-if)#show configuration failed 
Sat Aug 29 11:00:58.873 UTC
!! SEMANTIC ERRORS: This configuration was rejected by 
!! the system due to semantic errors. The individual 
!! errors with each failed configuration command can be 
!! found below.


interface MgmtEth0/RP0/CPU0/0
 vrf LAB_MGMT
!!% 'RSI' detected the 'fatal' condition 'The interface's numbered and unnumbered IPv4/IPv6 addresses must be removed prior to changing or deleting the VRF'
!
end

```

```
RP/0/RP0/CPU0:XRV1(config-if)#no  ipv4 address 192.168.250.71 255.255.255.0
RP/0/RP0/CPU0:XRV1(config-if)#commit
```


```
RP/0/RP0/CPU0:XRV1(config-if)# ipv4 address 192.168.250.71 255.255.255.0   
RP/0/RP0/CPU0:XRV1(config-if)#commit
```

Add static routes to vrf

```
RP/0/RP0/CPU0:XRV1(config-if)#router static
RP/0/RP0/CPU0:XRV1(config-static)#vrf LAB_MGMT
RP/0/RP0/CPU0:XRV1(config-static-vrf)# address-family ipv4 unicast
RP/0/RP0/CPU0:XRV1(config-static-vrf-afi)#  0.0.0.0/0 Null0
RP/0/RP0/CPU0:XRV1(config-static-vrf-afi)#  192.168.199.3/32 192.168.250.1
RP/0/RP0/CPU0:XRV1(config-static-vrf-afi)#commit
```

show route vrf LAB_MGMT ipv4

```
RP/0/RP0/CPU0:XRV1(config-static-vrf-afi)#end
RP/0/RP0/CPU0:XRV1#show route vrf LAB_MGMT ipv4
Sat Aug 29 11:06:01.730 UTC

Codes: C - connected, S - static, R - RIP, B - BGP, (>) - Diversion path
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - ISIS, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, su - IS-IS summary null, * - candidate default
       U - per-user static route, o - ODR, L - local, G  - DAGR, l - LISP
       A - access/subscriber, a - Application route
       M - mobile route, r - RPL, t - Traffic Engineering, (!) - FRR Backup path

Gateway of last resort is 0.0.0.0 to network 0.0.0.0

S*   0.0.0.0/0 is directly connected, 00:01:22, Null0
S    192.168.199.3/32 [1/0] via 192.168.250.1, 00:01:22
C    192.168.250.0/24 is directly connected, 00:02:33, MgmtEth0/RP0/CPU0/0
L    192.168.250.71/32 is directly connected, 00:02:33, MgmtEth0/RP0/CPU0/0
```
