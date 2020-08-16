# ospf configuration 

CSR1

```
interface Loopback0
 ip address 192.51.100.1 255.255.255.255
 ip ospf 1 area 0

interface GigabitEthernet2.12
 encapsulation dot1Q 12
 ip address 192.51.100.200 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0

router ospf 1
 router-id 192.51.100.1
 passive-interface default
 no passive-interface GigabitEthernet2.12
```


CSR2

```
interface Loopback0
 ip address 192.51.100.1 255.255.255.255
 ip ospf 1 area 0

interface GigabitEthernet2.12
 encapsulation dot1Q 12
 ip address 192.51.100.201 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0

router ospf 1
 router-id 192.51.100.2
 passive-interface default
 no passive-interface GigabitEthernet2.12
```
