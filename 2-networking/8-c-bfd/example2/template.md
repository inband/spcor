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

Verify on CSR1

```
CSR1#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.201                          1/1          Up        Up        Gi2.12
192.51.100.203                          2/2          Up        Up        Gi3.13

```



Setup and verify on CSR2

NOTE: CSR2 to CSR4 configured in Example1  (without template)

```
CSR2#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.200                          1/1          Up        Up        Gi2.12
192.51.100.205                       4097/4097       Up        Up        Gi3.24

```


Setup and verify on CSR3

```
CSR3#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.202                          2/2          Up        Up        Gi2.13
192.51.100.207                          1/1          Up        Up        Gi3.34

```

Setup and verify on CSR4

NOTE: CSR4 to CSR2 configured in Example1 (without template)

```
CSR4#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.204                       4097/4097       Up        Up        Gi2.24
192.51.100.206                          1/1          Up        Up        Gi3.34


```
