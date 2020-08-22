# end of exapmle2 config


### csr1


```
CSR1#show running-config 
Building configuration...

Current configuration : 2986 bytes
!
! Last configuration change at 02:35:44 UTC Sat Aug 22 2020 by admin
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no platform punt-keepalive disable-kernel-core
platform console virtual
!
hostname CSR1
!
boot-start-marker
boot-end-marker
!
!
vrf definition LAB_MGMT
 rd 2000:1000
 !
 address-family ipv4
  route-target export 2000:1000
  route-target import 2000:1000
 exit-address-family
!
!
aaa new-model
!
!
!
!
!
!
!
aaa session-id common
!
!
!
!
!
!
!
!
!


ip domain name lab.local

!
!
!
!
!
!
!
!
!
!
subscriber templating
!
multilink bundle-name authenticated
!
!
spanning-tree extend system-id
!
username admin privilege 15 secret 5 $
!         
redundancy
 mode none
bfd-template single-hop BFD_TEMPLATE
 interval min-tx 50 min-rx 50 multiplier 5
!
!
!
!
!
!
!
ip ssh version 2
! 
!
!
!
!
!
!
!
!
!
!
!
!
! 
! 
!
interface Loopback0
 ip address 192.51.100.1 255.255.255.255
 ip ospf 1 area 0
!
interface Loopback5
 ip address 192.51.100.5 255.255.255.255
!
interface GigabitEthernet1
 vrf forwarding LAB_MGMT
 ip address 192.168.250.31 255.255.255.0
 negotiation auto
!
interface GigabitEthernet2
 no ip address
 negotiation auto
!
interface GigabitEthernet2.12
 encapsulation dot1Q 12
 ip address 192.51.100.200 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
 bfd template BFD_TEMPLATE
!
interface GigabitEthernet3
 no ip address
 negotiation auto
!
interface GigabitEthernet3.13
 encapsulation dot1Q 13
 ip address 192.51.100.202 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
 bfd template BFD_TEMPLATE
!
interface GigabitEthernet4
 no ip address
 negotiation auto
!         
interface GigabitEthernet4.10
 encapsulation dot1Q 10
 ip address 192.51.100.11 255.255.255.254
!
router ospf 1
 router-id 192.51.100.1
 passive-interface default
 no passive-interface GigabitEthernet2.12
 no passive-interface GigabitEthernet3.13
!
router bgp 64499
 bgp log-neighbor-changes
 network 192.51.100.0
 redistribute connected
 neighbor 192.51.100.2 remote-as 64499
 neighbor 192.51.100.2 update-source Loopback0
 neighbor 192.51.100.2 prefix-list PL_IBGP_OUT out
 neighbor 192.51.100.10 remote-as 64496
 neighbor 192.51.100.10 update-source GigabitEthernet4.10
 neighbor 192.51.100.10 prefix-list PL_EBGP_OUT out
!
!
virtual-service csr_mgmt
!
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route 192.51.100.0 255.255.255.0 Null0
ip route vrf LAB_MGMT 192.168.199.3 255.255.255.255 192.168.250.1
!
!
ip prefix-list PL_EBGP_OUT seq 10 permit 192.51.100.0/24
ip prefix-list PL_EBGP_OUT seq 1000 deny 0.0.0.0/0 le 32
!
ip prefix-list PL_IBGP_OUT seq 10 permit 192.51.100.0/24 le 32
ip prefix-list PL_IBGP_OUT seq 1000 deny 0.0.0.0/0 le 32
!
!
!
!
!
control-plane
!
!
line con 0
 stopbits 1
line vty 0 5
 exec-timeout 600 0
 privilege level 15
 transport input ssh
!
!
end

CSR1# 


```


### csr2

```
CSR2#show run
Building configuration...

Current configuration : 2538 bytes
!
! Last configuration change at 02:38:46 UTC Sat Aug 22 2020 by admin
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no platform punt-keepalive disable-kernel-core
platform console virtual
!
hostname CSR2
!
boot-start-marker
boot-end-marker
!
!
vrf definition LAB_MGMT
 rd 2000:1000
 !
 address-family ipv4
  route-target export 2000:1000
  route-target import 2000:1000
 exit-address-family
!
!
aaa new-model
!
!
aaa authorization exec default local 
!
!
!
!
!         
aaa session-id common
!
!
!
!
!
!
!
!
!


ip domain name lab.local

!
!
!
!
!
!
!
!
!
!
subscriber templating
!
multilink bundle-name authenticated
!
!
spanning-tree extend system-id
!
username admin privilege 15 secret 5 $
!
redundancy
 mode none
bfd-template single-hop BFD_TEMPLATE
 interval min-tx 50 min-rx 50 multiplier 5
!
!
!
!
!
!
!
ip ssh version 2
ip scp server enable
! 
!
!
!
!
!
!
!
!
!
!
!
!
! 
! 
!
interface Loopback0
 ip address 192.51.100.2 255.255.255.255
 ip ospf 1 area 0
!         
interface GigabitEthernet1
 vrf forwarding LAB_MGMT
 ip address 192.168.250.32 255.255.255.0
 negotiation auto
!
interface GigabitEthernet2
 no ip address
 negotiation auto
!
interface GigabitEthernet2.12
 encapsulation dot1Q 12
 ip address 192.51.100.201 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
 bfd template BFD_TEMPLATE
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
 bfd interval 50 min_rx 50 multiplier 5
!
router ospf 1
 router-id 192.51.100.2
 passive-interface default
 no passive-interface GigabitEthernet2.12
 no passive-interface GigabitEthernet3.24
 bfd all-interfaces
!
router bgp 64499
 bgp log-neighbor-changes
 neighbor 192.51.100.1 remote-as 64499
 neighbor 192.51.100.1 update-source Loopback0
!
!
virtual-service csr_mgmt
!
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route 192.51.100.0 255.255.255.0 Null0
ip route vrf LAB_MGMT 192.168.199.3 255.255.255.255 192.168.250.1
!
!
snmp-server source-interface informs Loopback0
!
!
!
!
control-plane
!
!
line con 0
 stopbits 1
line vty 0
 exec-timeout 600 0
 privilege level 15
 transport input ssh
line vty 1
 exec-timeout 600 0
 length 0 
 transport input ssh
line vty 2 5
 exec-timeout 600 0
 privilege level 15
 transport input ssh
!
!
end

CSR2# 

```



### csr3

```
CSR3#show run
Building configuration...

Current configuration : 2239 bytes
!
! Last configuration change at 02:38:56 UTC Sat Aug 22 2020 by admin
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no platform punt-keepalive disable-kernel-core
platform console virtual
!
hostname CSR3
!
boot-start-marker
boot-end-marker
!
!
vrf definition LAB_MGMT
 rd 2000:1000
 !
 address-family ipv4
  route-target export 2000:1000
  route-target import 2000:1000
 exit-address-family
!
!
aaa new-model
!
!
!
!         
!
!
!
aaa session-id common
!
!
!
!
!
!
!
!
!


ip domain name lab.local

!
!
!
!
!
!
!
!
!
!
subscriber templating
!
multilink bundle-name authenticated
!
!
spanning-tree extend system-id
!
username admin privilege 15 secret 5 $
!
redundancy
 mode none
bfd-template single-hop BFD_TEMPLATE
 interval min-tx 50 min-rx 50 multiplier 5
!
!
!
!
!
!
!
ip ssh version 2
! 
!
!
!
!
!
!
!
!
!         
!
!
!
! 
! 
!
interface Loopback0
 ip address 192.51.100.3 255.255.255.255
 ip ospf 1 area 0
!
interface GigabitEthernet1
 vrf forwarding LAB_MGMT
 ip address 192.168.250.33 255.255.255.0
 negotiation auto
!
interface GigabitEthernet2
 no ip address
 negotiation auto
!
interface GigabitEthernet2.13
 encapsulation dot1Q 13
 ip address 192.51.100.203 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
 bfd template BFD_TEMPLATE
!
interface GigabitEthernet3
 no ip address
 negotiation auto
!
interface GigabitEthernet3.34
 encapsulation dot1Q 34
 ip address 192.51.100.206 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
 bfd template BFD_TEMPLATE
!
router ospf 1
 router-id 192.51.100.3
 passive-interface default
 no passive-interface GigabitEthernet2.13
 no passive-interface GigabitEthernet3.34
!
!
virtual-service csr_mgmt
!
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route vrf LAB_MGMT 192.168.199.3 255.255.255.255 192.168.250.1
!
!
!
!
!
!
control-plane
!
!
line con 0
 stopbits 1
line vty 0
 exec-timeout 600 0
 privilege level 15
 transport input ssh
line vty 1
 exec-timeout 600 0
 length 0
 transport input ssh
line vty 2 5
 exec-timeout 600 0
 privilege level 15
 transport input ssh
!
!
end

CSR3# 

```



### csr4

```
CSR4#show run
Building configuration...

Current configuration : 2259 bytes
!
! Last configuration change at 02:38:39 UTC Sat Aug 22 2020 by admin
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no platform punt-keepalive disable-kernel-core
platform console virtual
!
hostname CSR4
!
boot-start-marker
boot-end-marker
!
!
vrf definition LAB_MGMT
 rd 2000:1000
 !
 address-family ipv4
  route-target export 2000:1000
  route-target import 2000:1000
 exit-address-family
!
!
aaa new-model
!
!
!
!
!
!
!
aaa session-id common
!
!
!
!
!
!
!
!
!


ip domain name lab.local

!
!
!
!
!         
!
!
!
!
!
subscriber templating
!
multilink bundle-name authenticated
!
!
spanning-tree extend system-id
!
username admin privilege 15 secret 5 $
!
redundancy
 mode none
bfd-template single-hop BFD_TEMPLATE
 interval min-tx 50 min-rx 50 multiplier 5
!
!
!
!
!
!         
!
ip ssh version 2
! 
!
!
!
!
!
!
!
!
!
!
!
!
! 
! 
!
interface Loopback0
 ip address 192.51.100.4 255.255.255.255
 ip ospf 1 area 0
!
interface GigabitEthernet1
 vrf forwarding LAB_MGMT
 ip address 192.168.250.34 255.255.255.0
 negotiation auto
!
interface GigabitEthernet2
 no ip address
 negotiation auto
!
interface GigabitEthernet2.24
 encapsulation dot1Q 24
 ip address 192.51.100.205 255.255.255.254
 ip ospf network point-to-point
 ip ospf 1 area 0
 bfd interval 50 min_rx 50 multiplier 5
!
interface GigabitEthernet3
 no ip address
 negotiation auto
!
interface GigabitEthernet3.34
 encapsulation dot1Q 34
 ip address 192.51.100.207 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
 bfd template BFD_TEMPLATE
!
router ospf 1
 router-id 192.51.100.4
 passive-interface default
 no passive-interface GigabitEthernet2.24
 no passive-interface GigabitEthernet3.34
 bfd all-interfaces
!
!
virtual-service csr_mgmt
!
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route vrf LAB_MGMT 192.168.199.3 255.255.255.255 192.168.250.1
!
!
!
!
!
!
control-plane
!
!
line con 0
 stopbits 1
line vty 0
 exec-timeout 600 0
 privilege level 15
 transport input ssh
line vty 1
 exec-timeout 600 0
 length 0
 transport input ssh
line vty 2 5
 exec-timeout 600 0
 privilege level 15
 transport input ssh
!
!
end

CSR4#

```
