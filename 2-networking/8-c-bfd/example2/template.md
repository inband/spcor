# bfd-template


Configure BFD template globally and apply to OSPF interfaces

```
hostname CSR1
!
bfd-template single-hop BFD_TEMPLATE
 interval min-tx 50 min-rx 50 multiplier 5
!
!
interface GigabitEthernet2.12
 encapsulation dot1Q 12
 ip address 192.51.100.200 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
 bfd template BFD_TEMPLATE
!
interface GigabitEthernet3.13
 encapsulation dot1Q 13
 ip address 192.51.100.202 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
 bfd template BFD_TEMPLATE
!
router ospf 1
 router-id 192.51.100.1
 passive-interface default
 no passive-interface GigabitEthernet2.12
 no passive-interface GigabitEthernet3.13
!
```

