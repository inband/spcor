


```
RP/0/RP0/CPU0:lab-xrv0#show run
Sat Apr  4 02:42:10.498 UTC
Building configuration...
!! IOS XR Configuration 6.6.3
!! Last configuration change at Fri Apr  3 22:52:53 2026 by admin
!
hostname lab-xrv0
username admin
 group root-lr
 group cisco-support
 secret 5 $1$SSls$3bG1dtIizQYDFqmJeGUUo0
!
aaa group server radius RAD-GRP
 vrf LAB-MGMT
 server-private 192.168.250.134 auth-port 1812 acct-port 1813
  key 7 021201481F0F0126
 !
 source-interface MgmtEth0/RP0/CPU0/0
!
aaa authorization network default group RAD-GRP
aaa authentication ppp default group RAD-GRP
vrf LAB-MGMT
 address-family ipv4 unicast
 !
!
pool vrf default ipv4 POOL1
 network 192.0.2.0/24
 exclude 192.0.2.0 192.0.2.255
!
pool vrf default ipv4 POOL2
 network 198.51.100.0/24
 exclude 198.51.100.0 198.51.100.255
!
dhcp ipv4
 profile TEST server
  broadcast-flag policy Check
  class CLASS-192-0-2-0
   lease 0 0 15
   pool POOL1
   dns-server 1.1.1.1 8.8.8.8
   subnet-mask 255.255.255.0
   default-router 192.0.2.2
  !
  class CLASS-198-51-100-0
   lease 0 0 15
   pool POOL2
   dns-server 1.1.1.1 8.8.8.8
   subnet-mask 255.255.255.0
   default-router 198.51.100.1
  !       
 !
 interface GigabitEthernet0/0/0/0.100 server profile TEST
!
call-home
 service active
 contact smart-licensing
 profile CiscoTAC-1
  active
  destination transport-method http
 !
!
interface Loopback0
 ipv4 address 7.7.7.7 255.255.255.255
!
interface Loopback100
 ipv4 address 192.0.2.2 255.255.255.0
 ipv4 address 198.51.100.1 255.255.255.0 secondary
!
interface MgmtEth0/RP0/CPU0/0
 vrf LAB-MGMT
 ipv4 address 192.168.250.52 255.255.255.0
!
interface GigabitEthernet0/0/0/0.100
 ipv4 mtu 1500
 ipv4 point-to-point
 ipv4 unnumbered Loopback100
 arp learning disable
 service-policy type control subscriber CP-203
 pppoe enable bba-group PPPoE-BBA-GRP1
 ipsubscriber ipv4 l2-connected
  initiator dhcp
  initiator unclassified-source
 !
 encapsulation ambiguous dot1q 100 second-dot1q any
!
dynamic-template
 type ppp PPP-DEFAULT
  ppp authentication chap pap
  keepalive 20 3
  ppp ipcp dns 1.1.1.1 8.8.8.8
  ipv4 unnumbered Loopback0
 !
 type ipsubscriber IPOE-BASE
  accounting aaa list default type session periodic-interval 5
  ipv4 unnumbered Loopback100
 !        
!
aaa attribute format ATTR_MAC
 mac-address
!
aaa accounting subscriber default group RAD-GRP
aaa authorization subscriber default group RAD-GRP
aaa authentication subscriber default group RAD-GRP
subscriber
 arp scale-mode-enable
 arp uncond-proxy-arp-enable
 pta tcp mss-adjust 1492
 redundancy
  source-interface Loopback0
  group 1
   peer 10.2.2.2
   state-control-route ipv4 192.168.17.0/24 vrf default tag 1
   interface-list
   !
  !
 !
!
pppoe bba-group PPPoE-BBA-GRP1
 pado delay 0
!
class-map type control subscriber match-any PPPOE
 match protocol ppp 
 end-class-map
! 
!
class-map type control subscriber match-any DHCPV4
 match protocol dhcpv4 
 end-class-map
! 
policy-map type control subscriber CP-203
 event session-start match-first
  class type control subscriber DHCPV4 do-until-failure
   1 authorize aaa list default format ATTR_MAC password cisco
   2 activate dynamic-template IPOE-BASE
  ! 
  class type control subscriber PPPOE do-all
   1 activate dynamic-template PPP-DEFAULT
  ! 
 ! 
 event session-activate match-first
  class type control subscriber PPPOE do-until-failure
   1 authenticate aaa list default
  ! 
 ! 
 end-policy-map
! 
end
```
