# config


### csr1
```
interface GigabitEthernet2
 no ip address
 negotiation auto
!
interface GigabitEthernet2.12
 encapsulation dot1Q 12
 ip address 192.51.100.200 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0
!
interface GigabitEthernet3
 no ip address
 negotiation auto
!
interface GigabitEthernet3.13
 encapsulation dot1Q 13
 ip address 192.51.100.202 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0
!
router ospf 1
 router-id 192.51.100.1
 passive-interface default
 no passive-interface GigabitEthernet2.12
 no passive-interface GigabitEthernet3.13
!
```


### csr2
```
interface GigabitEthernet2
 no ip address
 negotiation auto
!
interface GigabitEthernet2.12
 encapsulation dot1Q 12
 ip address 192.51.100.201 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0
!
interface GigabitEthernet3
 no ip address
 negotiation auto
!
interface GigabitEthernet3.24
 encapsulation dot1Q 24
 ip address 192.51.100.204 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0
!
router ospf 1
 router-id 192.51.100.2
 passive-interface default
 no passive-interface GigabitEthernet2.12
 no passive-interface GigabitEthernet3.24
!

```


### csr3
```
interface GigabitEthernet2
 no ip address
 negotiation auto
!
interface GigabitEthernet2.13
 encapsulation dot1Q 13
 ip address 192.51.100.203 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0
!
interface GigabitEthernet3
 no ip address
 negotiation auto
!
interface GigabitEthernet3.34
 encapsulation dot1Q 34
 ip address 192.51.100.206 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0
!
router ospf 1
 router-id 192.51.100.3
 passive-interface default
 no passive-interface GigabitEthernet2.13
 no passive-interface GigabitEthernet3.34
!

```


### csr4
```
interface GigabitEthernet2
 no ip address
 negotiation auto
!
interface GigabitEthernet2.24
 encapsulation dot1Q 24
 ip address 192.51.100.205 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0
!
interface GigabitEthernet3
 no ip address
 negotiation auto
!
interface GigabitEthernet3.34
 encapsulation dot1Q 34
 ip address 192.51.100.207 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0
!
router ospf 1
 router-id 192.51.100.4
 passive-interface default
 no passive-interface GigabitEthernet2.24
 no passive-interface GigabitEthernet3.34
!


```
