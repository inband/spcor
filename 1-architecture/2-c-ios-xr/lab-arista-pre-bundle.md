
```
lab-arista-pe1#
lab-arista-pe1#show run
! Command: show running-config
! device: lab-arista-pe1 (vEOS-lab, EOS-4.32.1F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname lab-arista-pe1
!
spanning-tree mode mstp
!
system l1
   unsupported speed action error
   unsupported error-correction action error
!
vlan 105
!
interface Ethernet1
   no switchport
   ip address 10.3.4.1/30
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   no switchport
   ip address 10.1.3.2/30
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2.100
   encapsulation dot1q vlan 100
!
interface Ethernet3
   no switchport
!
interface Ethernet3.100
   encapsulation dot1q vlan 100
!
interface Ethernet4
!
interface Ethernet5
   switchport trunk allowed vlan 105
   switchport mode trunk
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.3.3.3/32
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan105
   ip address 10.10.105.99/24
!
ip routing
!
mpls ip
!
mpls ldp
   router-id interface Loopback0
   transport-address interface Loopback0
   no shutdown
!
patch panel
   patch CKT-PE1-BNG1
      connector 1 interface Ethernet2.100
      connector 2 interface Ethernet3.100
!
router bgp 65000
   router-id 10.3.3.3
   neighbor 10.4.4.4 remote-as 65000
   neighbor 10.4.4.4 update-source Loopback0
   neighbor 10.4.4.4 send-community extended
   !
   address-family evpn
      neighbor 10.4.4.4 activate
      neighbor 10.4.4.4 encapsulation mpls next-hop-self source-interface Loopback0
!
router ospf 1
   router-id 10.3.3.3
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
!
end
lab-arista-pe1#
```

```
lab-arista-pe2#show run
! Command: show running-config
 ! device: lab-arista-pe2 (vEOS-lab, EOS-4.32.1F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname lab-arista-pe2
!
spanning-tree mode mstp
!
system l1
   unsupported speed action error
   unsupported error-correction action error
!
vlan 105
!
vrf instance CONTROL
!
interface Ethernet1
   no switchport
   ip address 10.3.4.2/30
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   no switchport
   encapsulation dot1q vlan 762
   ip address 10.2.4.2/30
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2.100
   encapsulation dot1q vlan 100
!
interface Ethernet2.4090
   encapsulation dot1q vlan 4090
   vrf CONTROL
   ip address 169.254.1.1/30
   bfd interval 50 min-rx 50 multiplier 3
   bfd echo
   bfd static neighbor 169.254.1.2
!
interface Ethernet3
   no switchport
!
interface Ethernet3.100
   encapsulation dot1q vlan 100
!
interface Ethernet4
!
interface Ethernet5
   switchport trunk allowed vlan 105
   switchport mode trunk
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.4.4.4/32
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan105
   ip address 10.10.105.99/24
!
ip routing
ip routing vrf CONTROL
!
mpls ip
!
mpls ldp
   router-id interface Loopback0
   transport-address interface Loopback0
   no shutdown
!
patch panel
   patch CKT-PE2-BNG2
      connector 1 interface Ethernet2.100
      connector 2 interface Ethernet3.100
!
router bfd
   vrf CONTROL
      peer 169.254.1.2
!
router ospf 1
   router-id 10.4.4.4
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   max-lsa 12000
!
end
lab-arista-pe2#


```


