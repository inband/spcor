# Adding interfaces


Add to vmx.conf


Stop


Install (automatically start)


Check status


Bind status

Re-bind




The default number of ports on xMX is 10.  However this can be increased - to 96 virtio in lite mode.  [Juniper link](https://www.juniper.net/documentation/en_US/vmx/topics/task/configuration/vmx-chassis-active-ports-configuring.html#:~:text=You%20can%20specify%20the%20number,added%20to%20the%20VFP%20VM.)


```
root@VMX1# set chassis fpc 0 pic 0 number-of-ports 15 
```
```
root@VMX1# run show interfaces terse | match ge 
ge-0/0/0                up    up
ge-0/0/0.0              up    up   inet     192.168.222.80/24
ge-0/0/1                up    up
ge-0/0/1.0              up    up   inet     10.0.0.0/31     
ge-0/0/2                up    up
ge-0/0/2.1215           up    up   bridge  
ge-0/0/2.32767          up    up   multiservice
ge-0/0/3                up    up
ge-0/0/3.16             up    up   inet     10.0.0.0/31     
ge-0/0/3.32767          up    up   multiservice
ge-0/0/4                up    up
ge-0/0/4.47             up    up   inet     10.0.0.19/31    
ge-0/0/4.32767          up    up   multiservice
ge-0/0/5                up    up
ge-0/0/6                up    up
ge-0/0/7                up    up
ge-0/0/8                up    up
ge-0/0/9                up    up
ge-0/0/10               up    up
ge-0/0/11               up    up
ge-0/0/12               up    up
ge-0/0/13               up    up
ge-0/0/14               up    up
```


-------------------------

Backup

```
root@VMX1> show configuration | display set 
set version 18.2R1.9
set system root-authentication encrypted-password "*******************"
set system host-name VMX1
set system services ssh root-login allow
set system syslog user * any emergency
set system syslog user * pfe none
set system syslog file messages any notice
set system syslog file messages authorization info
set system syslog file interactive-commands interactive-commands any
set system processes dhcp-service traceoptions file dhcp_logfile
set system processes dhcp-service traceoptions file size 10m
set system processes dhcp-service traceoptions level all
set system processes dhcp-service traceoptions flag packet
set logical-systems CE1 interfaces lt-0/0/0 unit 211 encapsulation vlan
set logical-systems CE1 interfaces lt-0/0/0 unit 211 vlan-id 112
set logical-systems CE1 interfaces lt-0/0/0 unit 211 peer-unit 112
set logical-systems CE1 interfaces lt-0/0/0 unit 211 family inet address 10.1.0.0/31
set logical-systems CE1 interfaces lt-0/0/0 unit 2122 encapsulation vlan
set logical-systems CE1 interfaces lt-0/0/0 unit 2122 vlan-id 2122
set logical-systems CE1 interfaces lt-0/0/0 unit 2122 peer-unit 2221
set logical-systems CE1 interfaces lt-0/0/0 unit 2122 family inet address 10.1.12.1/24
set logical-systems CE1 interfaces ge-0/0/10 unit 621 vlan-id 621
set logical-systems CE1 interfaces ge-0/0/10 unit 621 family inet address 10.1.0.4/31
set logical-systems CE1 interfaces lo0 unit 21 family inet address 192.168.10.1/32
set logical-systems CE1 protocols bgp group iBGP type internal
set logical-systems CE1 protocols bgp group iBGP local-address 192.168.10.1
set logical-systems CE1 protocols bgp group iBGP family inet unicast add-path receive
set logical-systems CE1 protocols bgp group iBGP family inet unicast add-path send path-count 6
set logical-systems CE1 protocols bgp group iBGP export PL-iBGP-OUT
set logical-systems CE1 protocols bgp group iBGP neighbor 192.168.10.2
set logical-systems CE1 protocols bgp group eBGP family inet unicast
set logical-systems CE1 protocols bgp group eBGP peer-as 65000
set logical-systems CE1 protocols bgp group eBGP neighbor 10.1.0.1 export ADV-LOOPBACK
set logical-systems CE1 protocols bgp group eBGP neighbor 10.1.0.5 export ADV-LOOPBACK
set logical-systems CE1 protocols ospf area 0.0.0.0 interface lt-0/0/0.2122
set logical-systems CE1 protocols ospf area 0.0.0.0 interface lo0.21
set logical-systems CE1 policy-options policy-statement ADV-LOOPBACK term 1 from protocol direct
set logical-systems CE1 policy-options policy-statement ADV-LOOPBACK term 1 from protocol local
set logical-systems CE1 policy-options policy-statement ADV-LOOPBACK term 1 from protocol ospf
set logical-systems CE1 policy-options policy-statement ADV-LOOPBACK term 1 from route-filter 192.168.10.0/32 orlonger
set logical-systems CE1 policy-options policy-statement ADV-LOOPBACK term 1 then accept
set logical-systems CE1 policy-options policy-statement PL-iBGP-OUT term NHS from family inet
set logical-systems CE1 policy-options policy-statement PL-iBGP-OUT term NHS then next-hop self
set logical-systems CE1 routing-options autonomous-system 65001
set logical-systems CE2 interfaces lt-0/0/0 unit 221 encapsulation vlan
set logical-systems CE2 interfaces lt-0/0/0 unit 221 vlan-id 122
set logical-systems CE2 interfaces lt-0/0/0 unit 221 peer-unit 122
set logical-systems CE2 interfaces lt-0/0/0 unit 221 family inet address 10.1.0.6/31
set logical-systems CE2 interfaces lt-0/0/0 unit 2221 encapsulation vlan
set logical-systems CE2 interfaces lt-0/0/0 unit 2221 vlan-id 2122
set logical-systems CE2 interfaces lt-0/0/0 unit 2221 peer-unit 2122
set logical-systems CE2 interfaces lt-0/0/0 unit 2221 family inet address 10.1.12.2/24
set logical-systems CE2 interfaces ge-0/0/11 unit 622 vlan-id 622
set logical-systems CE2 interfaces ge-0/0/11 unit 622 family inet address 10.1.0.2/31
set logical-systems CE2 interfaces lo0 unit 22 family inet address 192.168.10.2/32
set logical-systems CE2 protocols bgp group iBGP type internal
set logical-systems CE2 protocols bgp group iBGP local-address 192.168.10.2
set logical-systems CE2 protocols bgp group iBGP family inet unicast add-path receive
set logical-systems CE2 protocols bgp group iBGP family inet unicast add-path send path-count 6
set logical-systems CE2 protocols bgp group iBGP export PL-iBGP-OUT
set logical-systems CE2 protocols bgp group iBGP neighbor 192.168.10.1
set logical-systems CE2 protocols bgp group eBGP family inet unicast
set logical-systems CE2 protocols bgp group eBGP peer-as 65000
set logical-systems CE2 protocols bgp group eBGP neighbor 10.1.0.7 export ADV-LOOPBACK
set logical-systems CE2 protocols bgp group eBGP neighbor 10.1.0.3 export ADV-LOOPBACK
set logical-systems CE2 protocols ospf area 0.0.0.0 interface lt-0/0/0.2221
set logical-systems CE2 protocols ospf area 0.0.0.0 interface lo0.22
set logical-systems CE2 policy-options policy-statement ADV-LOOPBACK term 1 from protocol direct
set logical-systems CE2 policy-options policy-statement ADV-LOOPBACK term 1 from protocol local
set logical-systems CE2 policy-options policy-statement ADV-LOOPBACK term 1 from protocol ospf
set logical-systems CE2 policy-options policy-statement ADV-LOOPBACK term 1 from route-filter 192.168.10.0/24 orlonger
set logical-systems CE2 policy-options policy-statement ADV-LOOPBACK term 1 then accept
set logical-systems CE2 policy-options policy-statement PL-iBGP-OUT term NHS from family inet
set logical-systems CE2 policy-options policy-statement PL-iBGP-OUT term NHS then next-hop self
set logical-systems CE2 routing-options autonomous-system 65001
set logical-systems P1 interfaces lt-0/0/0 unit 21 encapsulation vlan
set logical-systems P1 interfaces lt-0/0/0 unit 21 vlan-id 12
set logical-systems P1 interfaces lt-0/0/0 unit 21 peer-unit 12
set logical-systems P1 interfaces lt-0/0/0 unit 21 family inet address 10.0.0.3/31
set logical-systems P1 interfaces lt-0/0/0 unit 21 family iso
set logical-systems P1 interfaces lt-0/0/0 unit 21 family mpls
set logical-systems P1 interfaces lt-0/0/0 unit 23 encapsulation vlan
set logical-systems P1 interfaces lt-0/0/0 unit 23 vlan-id 23
set logical-systems P1 interfaces lt-0/0/0 unit 23 peer-unit 32
set logical-systems P1 interfaces lt-0/0/0 unit 23 family inet address 10.0.0.8/31
set logical-systems P1 interfaces lt-0/0/0 unit 23 family iso
set logical-systems P1 interfaces lt-0/0/0 unit 23 family mpls
set logical-systems P1 interfaces lt-0/0/0 unit 24 encapsulation vlan
set logical-systems P1 interfaces lt-0/0/0 unit 24 vlan-id 24
set logical-systems P1 interfaces lt-0/0/0 unit 24 peer-unit 42
set logical-systems P1 interfaces lt-0/0/0 unit 24 family inet address 10.0.0.16/31
set logical-systems P1 interfaces lt-0/0/0 unit 24 family iso
set logical-systems P1 interfaces lt-0/0/0 unit 24 family mpls
set logical-systems P1 interfaces ge-0/0/6 unit 27 vlan-id 27
set logical-systems P1 interfaces ge-0/0/6 unit 27 family inet address 10.0.0.6/31
set logical-systems P1 interfaces ge-0/0/6 unit 27 family iso
set logical-systems P1 interfaces ge-0/0/6 unit 27 family mpls
set logical-systems P1 interfaces ge-0/0/7 unit 227 vlan-id 227
set logical-systems P1 interfaces ge-0/0/7 unit 227 family inet address 10.0.0.24/31
set logical-systems P1 interfaces ge-0/0/7 unit 227 family iso
set logical-systems P1 interfaces ge-0/0/7 unit 227 family mpls
set logical-systems P1 interfaces ge-0/0/8 unit 25 vlan-id 25
set logical-systems P1 interfaces ge-0/0/8 unit 25 family inet address 10.0.0.20/31
set logical-systems P1 interfaces ge-0/0/8 unit 25 family iso
set logical-systems P1 interfaces ge-0/0/8 unit 25 family mpls
set logical-systems P1 interfaces lo0 unit 2 family inet address 172.16.0.1/32
set logical-systems P1 interfaces lo0 unit 2 family iso address 49.0000.0000.0002.00
set logical-systems P1 protocols mpls icmp-tunneling
set logical-systems P1 protocols mpls interface lt-0/0/0.21
set logical-systems P1 protocols mpls interface lt-0/0/0.23
set logical-systems P1 protocols mpls interface lt-0/0/0.24
set logical-systems P1 protocols mpls interface ge-0/0/6.27
set logical-systems P1 protocols mpls interface ge-0/0/7.227
set logical-systems P1 protocols mpls interface ge-0/0/8.25
set logical-systems P1 protocols isis level 2 wide-metrics-only
set logical-systems P1 protocols isis interface lt-0/0/0.21
set logical-systems P1 protocols isis interface lt-0/0/0.23
set logical-systems P1 protocols isis interface lt-0/0/0.24
set logical-systems P1 protocols isis interface ge-0/0/6.27
set logical-systems P1 protocols isis interface ge-0/0/7.227
set logical-systems P1 protocols isis interface ge-0/0/8.25
set logical-systems P1 protocols isis interface lo0.2
set logical-systems P1 protocols ldp track-igp-metric
set logical-systems P1 protocols ldp interface lt-0/0/0.21
set logical-systems P1 protocols ldp interface lt-0/0/0.23
set logical-systems P1 protocols ldp interface lt-0/0/0.24
set logical-systems P1 protocols ldp interface ge-0/0/6.27
set logical-systems P1 protocols ldp interface ge-0/0/7.227
set logical-systems P1 protocols ldp interface ge-0/0/8.25
set logical-systems P1 policy-options policy-statement LB then load-balance per-packet
set logical-systems P1 routing-options forwarding-table export LB
set logical-systems PE1 interfaces lt-0/0/0 unit 12 encapsulation vlan
set logical-systems PE1 interfaces lt-0/0/0 unit 12 vlan-id 12
set logical-systems PE1 interfaces lt-0/0/0 unit 12 peer-unit 21
set logical-systems PE1 interfaces lt-0/0/0 unit 12 family inet address 10.0.0.2/31
set logical-systems PE1 interfaces lt-0/0/0 unit 12 family iso
set logical-systems PE1 interfaces lt-0/0/0 unit 12 family mpls
set logical-systems PE1 interfaces lt-0/0/0 unit 112 encapsulation vlan
set logical-systems PE1 interfaces lt-0/0/0 unit 112 vlan-id 112
set logical-systems PE1 interfaces lt-0/0/0 unit 112 peer-unit 211
set logical-systems PE1 interfaces lt-0/0/0 unit 112 family inet address 10.1.0.1/31
set logical-systems PE1 interfaces lt-0/0/0 unit 122 encapsulation vlan
set logical-systems PE1 interfaces lt-0/0/0 unit 122 vlan-id 122
set logical-systems PE1 interfaces lt-0/0/0 unit 122 peer-unit 221
set logical-systems PE1 interfaces lt-0/0/0 unit 122 family inet address 10.1.0.7/31
set logical-systems PE1 interfaces ge-0/0/3 unit 16 vlan-id 16
set logical-systems PE1 interfaces ge-0/0/3 unit 16 family inet address 10.0.0.0/31
set logical-systems PE1 interfaces ge-0/0/3 unit 16 family iso
set logical-systems PE1 interfaces ge-0/0/3 unit 16 family mpls
set logical-systems PE1 interfaces lo0 unit 1 family inet address 172.16.0.11/32
set logical-systems PE1 interfaces lo0 unit 1 family iso address 49.0000.0000.0001.00
set logical-systems PE1 protocols mpls interface lt-0/0/0.12
set logical-systems PE1 protocols mpls interface ge-0/0/3.16
set logical-systems PE1 protocols bgp group iBGP-RR type internal
set logical-systems PE1 protocols bgp group iBGP-RR local-address 172.16.0.11
set logical-systems PE1 protocols bgp group iBGP-RR family inet unicast add-path receive
set logical-systems PE1 protocols bgp group iBGP-RR family inet unicast add-path send path-count 6
set logical-systems PE1 protocols bgp group iBGP-RR export PL-iBGP-RR-OUT
set logical-systems PE1 protocols bgp group iBGP-RR neighbor 172.16.0.201
set logical-systems PE1 protocols bgp group iBGP-RR neighbor 172.16.0.202
set logical-systems PE1 protocols bgp group eBGP-65001 family inet unicast
set logical-systems PE1 protocols bgp group eBGP-65001 peer-as 65001
set logical-systems PE1 protocols bgp group eBGP-65001 neighbor 10.1.0.0 export PL-eBGP-65001-CE1-OUT
set logical-systems PE1 protocols bgp group eBGP-65001 neighbor 10.1.0.0 export ADV-LOOPBACK
set logical-systems PE1 protocols bgp group eBGP-65001 neighbor 10.1.0.6 export PL-eBGP-65001-CE2-OUT
set logical-systems PE1 protocols bgp group eBGP-65001 neighbor 10.1.0.6 export ADV-LOOPBACK
set logical-systems PE1 protocols isis level 2 wide-metrics-only
set logical-systems PE1 protocols isis interface lt-0/0/0.12
set logical-systems PE1 protocols isis interface ge-0/0/3.16 level 2 metric 100
set logical-systems PE1 protocols isis interface lo0.1
set logical-systems PE1 protocols ldp track-igp-metric
set logical-systems PE1 protocols ldp interface lt-0/0/0.12
set logical-systems PE1 protocols ldp interface ge-0/0/3.16
set logical-systems PE1 policy-options policy-statement ADV-LOOPBACK term 1 from protocol direct
set logical-systems PE1 policy-options policy-statement ADV-LOOPBACK term 1 from protocol local
set logical-systems PE1 policy-options policy-statement ADV-LOOPBACK term 1 from protocol isis
set logical-systems PE1 policy-options policy-statement ADV-LOOPBACK term 1 from route-filter 172.16.0.0/24 orlonger
set logical-systems PE1 policy-options policy-statement ADV-LOOPBACK term 1 then accept
set logical-systems PE1 policy-options policy-statement PL-eBGP-65001-CE1-OUT term BGP then metric 100
set logical-systems PE1 policy-options policy-statement PL-eBGP-65001-CE2-OUT term BGP then metric 200
set logical-systems PE1 policy-options policy-statement PL-iBGP-RR-OUT term NHS from family inet
set logical-systems PE1 policy-options policy-statement PL-iBGP-RR-OUT term NHS then next-hop self
set logical-systems PE1 routing-options autonomous-system 65000
set logical-systems PE3 interfaces lt-0/0/0 unit 32 encapsulation vlan
set logical-systems PE3 interfaces lt-0/0/0 unit 32 vlan-id 23
set logical-systems PE3 interfaces lt-0/0/0 unit 32 peer-unit 23
set logical-systems PE3 interfaces lt-0/0/0 unit 32 family inet address 10.0.0.9/31
set logical-systems PE3 interfaces lt-0/0/0 unit 32 family iso
set logical-systems PE3 interfaces lt-0/0/0 unit 32 family mpls
set logical-systems PE3 interfaces ge-0/0/9 unit 38 vlan-id 38
set logical-systems PE3 interfaces ge-0/0/9 unit 38 family inet address 10.0.0.12/31
set logical-systems PE3 interfaces ge-0/0/9 unit 38 family iso
set logical-systems PE3 interfaces ge-0/0/9 unit 38 family mpls
set logical-systems PE3 interfaces ge-0/0/12 unit 1234 vlan-id 1234
set logical-systems PE3 interfaces ge-0/0/12 unit 1234 family inet address 10.2.0.33/24
set logical-systems PE3 interfaces lo0 unit 3 family inet address 172.16.0.33/32
set logical-systems PE3 interfaces lo0 unit 3 family iso address 49.0000.0000.0003.00
set logical-systems PE3 protocols mpls interface lt-0/0/0.32
set logical-systems PE3 protocols mpls interface ge-0/0/9.38
set logical-systems PE3 protocols bgp group iBGP-RR type internal
set logical-systems PE3 protocols bgp group iBGP-RR local-address 172.16.0.33
set logical-systems PE3 protocols bgp group iBGP-RR family inet unicast add-path receive
set logical-systems PE3 protocols bgp group iBGP-RR family inet unicast add-path send path-count 6
set logical-systems PE3 protocols bgp group iBGP-RR export PL-iBGP-RR-OUT
set logical-systems PE3 protocols bgp group iBGP-RR neighbor 172.16.0.201
set logical-systems PE3 protocols bgp group iBGP-RR neighbor 172.16.0.202
set logical-systems PE3 protocols bgp group eBGP-65002 family inet unicast
set logical-systems PE3 protocols bgp group eBGP-65002 peer-as 65002
set logical-systems PE3 protocols bgp group eBGP-65002 neighbor 10.2.0.3 export PL-eBGP-65002-BR3-OUT
set logical-systems PE3 protocols bgp group eBGP-65002 neighbor 10.2.0.3 export ADV-LOOPBACK
set logical-systems PE3 protocols isis level 2 wide-metrics-only
set logical-systems PE3 protocols isis interface lt-0/0/0.32
set logical-systems PE3 protocols isis interface ge-0/0/9.38 level 2 metric 100
set logical-systems PE3 protocols isis interface lo0.3
set logical-systems PE3 protocols ldp track-igp-metric
set logical-systems PE3 protocols ldp interface lt-0/0/0.32
set logical-systems PE3 protocols ldp interface ge-0/0/9.38
set logical-systems PE3 policy-options policy-statement ADV-LOOPBACK term 1 from protocol direct
set logical-systems PE3 policy-options policy-statement ADV-LOOPBACK term 1 from protocol local
set logical-systems PE3 policy-options policy-statement ADV-LOOPBACK term 1 from protocol isis
set logical-systems PE3 policy-options policy-statement ADV-LOOPBACK term 1 from route-filter 172.16.0.0/24 orlonger
set logical-systems PE3 policy-options policy-statement ADV-LOOPBACK term 1 then accept
set logical-systems PE3 policy-options policy-statement PL-eBGP-65002-BR3-OUT term BGP then metric 100
set logical-systems PE3 policy-options policy-statement PL-iBGP-RR-OUT term NHS from family inet
set logical-systems PE3 policy-options policy-statement PL-iBGP-RR-OUT term NHS then next-hop self
set logical-systems PE3 routing-options autonomous-system 65000
set logical-systems RR1 interfaces lt-0/0/0 unit 42 encapsulation vlan
set logical-systems RR1 interfaces lt-0/0/0 unit 42 vlan-id 24
set logical-systems RR1 interfaces lt-0/0/0 unit 42 peer-unit 24
set logical-systems RR1 interfaces lt-0/0/0 unit 42 family inet address 10.0.0.17/31
set logical-systems RR1 interfaces lt-0/0/0 unit 42 family iso
set logical-systems RR1 interfaces lt-0/0/0 unit 42 family mpls
set logical-systems RR1 interfaces ge-0/0/4 unit 47 vlan-id 47
set logical-systems RR1 interfaces ge-0/0/4 unit 47 family inet address 10.0.0.19/31
set logical-systems RR1 interfaces ge-0/0/4 unit 47 family iso
set logical-systems RR1 interfaces ge-0/0/4 unit 47 family mpls
set logical-systems RR1 interfaces ge-0/0/5 unit 45 vlan-id 45
set logical-systems RR1 interfaces ge-0/0/5 unit 45 family inet address 10.0.0.14/31
set logical-systems RR1 interfaces ge-0/0/5 unit 45 family iso
set logical-systems RR1 interfaces ge-0/0/5 unit 45 family mpls
set logical-systems RR1 interfaces lo0 unit 4 family inet address 172.16.0.201/32
set logical-systems RR1 interfaces lo0 unit 4 family iso address 49.0000.0000.0004.00
set logical-systems RR1 protocols mpls interface lt-0/0/0.42
set logical-systems RR1 protocols mpls interface ge-0/0/4.47
set logical-systems RR1 protocols mpls interface ge-0/0/5.45
set logical-systems RR1 protocols bgp group iBGP-CLIENTS type internal
set logical-systems RR1 protocols bgp group iBGP-CLIENTS local-address 172.16.0.201
set logical-systems RR1 protocols bgp group iBGP-CLIENTS family inet unicast add-path receive
set logical-systems RR1 protocols bgp group iBGP-CLIENTS family inet unicast add-path send path-count 6
set logical-systems RR1 protocols bgp group iBGP-CLIENTS cluster 172.16.0.201
set logical-systems RR1 protocols bgp group iBGP-CLIENTS neighbor 172.16.0.11
set logical-systems RR1 protocols bgp group iBGP-CLIENTS neighbor 172.16.0.22
set logical-systems RR1 protocols bgp group iBGP-CLIENTS neighbor 172.16.0.33
set logical-systems RR1 protocols bgp group iBGP-CLIENTS neighbor 172.16.0.44
set logical-systems RR1 protocols bgp group iBGP-RR type internal
set logical-systems RR1 protocols bgp group iBGP-RR local-address 172.16.0.201
set logical-systems RR1 protocols bgp group iBGP-RR family inet unicast add-path receive
set logical-systems RR1 protocols bgp group iBGP-RR family inet unicast add-path send path-count 6
set logical-systems RR1 protocols bgp group iBGP-RR neighbor 172.16.0.202
set logical-systems RR1 protocols isis overload
set logical-systems RR1 protocols isis level 2 wide-metrics-only
set logical-systems RR1 protocols isis interface lt-0/0/0.42
set logical-systems RR1 protocols isis interface ge-0/0/4.47
set logical-systems RR1 protocols isis interface ge-0/0/5.45
set logical-systems RR1 protocols isis interface lo0.4
set logical-systems RR1 protocols ldp track-igp-metric
set logical-systems RR1 protocols ldp interface lt-0/0/0.42
set logical-systems RR1 protocols ldp interface ge-0/0/4.47
set logical-systems RR1 protocols ldp interface ge-0/0/5.45
set logical-systems RR1 routing-options autonomous-system 65000
set chassis fpc 0 pic 0 tunnel-services
set chassis fpc 0 pic 0 number-of-ports 15
set chassis fpc 0 lite-mode
set interfaces ge-0/0/0 unit 0 family inet address 192.168.222.80/24
set interfaces ge-0/0/1 unit 0 family inet address 10.0.0.0/31
set interfaces ge-0/0/1 unit 0 family mpls
set interfaces ge-0/0/2 vlan-tagging
set interfaces ge-0/0/2 encapsulation extended-vlan-bridge
set interfaces ge-0/0/2 unit 1215 vlan-id 1215
set interfaces ge-0/0/3 vlan-tagging
set interfaces ge-0/0/4 vlan-tagging
set interfaces ge-0/0/5 vlan-tagging
set interfaces ge-0/0/6 vlan-tagging
set interfaces ge-0/0/7 vlan-tagging
set interfaces ge-0/0/8 vlan-tagging
set interfaces ge-0/0/9 vlan-tagging    
set interfaces ge-0/0/10 vlan-tagging
set interfaces ge-0/0/11 vlan-tagging
set interfaces ge-0/0/12 vlan-tagging
set interfaces fxp0 unit 0 family inet dhcp vendor-id Juniper-vmx-VM5F9FE3B49D
set interfaces irb unit 1215 family inet address 10.12.15.15/24
set interfaces irb unit 1215 family mpls
set interfaces lo0 unit 0 family inet address 15.15.15.15/32
set interfaces lo0 unit 0 family inet6 address 2001::15/128
set routing-options static route 192.168.20.10/32 next-hop 192.168.222.1
set protocols mpls interface ge-0/0/1.0
set protocols mpls interface irb.1215
set protocols ospf area 0.0.0.0 interface ge-0/0/1.0
set protocols ospf area 0.0.0.0 interface lo0.0
set protocols ospf area 0.0.0.0 interface irb.1215
set protocols ldp track-igp-metric
set protocols ldp interface ge-0/0/1.0
set protocols ldp interface irb.1215
set bridge-domains vlan-1215 domain-type bridge
set bridge-domains vlan-1215 vlan-id 1215
set bridge-domains vlan-1215 interface ge-0/0/2.1215
set bridge-domains vlan-1215 routing-interface irb.1215
```
