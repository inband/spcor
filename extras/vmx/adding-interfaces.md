# Adding interfaces


Add to vmx.conf


Stop


Install (automatically start)


Check status


Bind status

Re-bind




-------------------------

Backup

```
root@VMX1> show configuration | display set 
set version 18.2R1.9
set system root-authentication encrypted-password "**************************"
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
set logical-systems CE1 interfaces lo0 unit 12 family inet address 192.168.10.1/32
set logical-systems PE1 interfaces lt-0/0/0 unit 12 encapsulation vlan
set logical-systems PE1 interfaces lt-0/0/0 unit 12 vlan-id 12
set logical-systems PE1 interfaces lt-0/0/0 unit 12 peer-unit 21
set logical-systems PE1 interfaces lt-0/0/0 unit 12 family inet address 10.0.0.2/31
set logical-systems PE1 interfaces lt-0/0/0 unit 12 family iso
set logical-systems PE1 interfaces lt-0/0/0 unit 112 encapsulation vlan
set logical-systems PE1 interfaces lt-0/0/0 unit 112 vlan-id 112
set logical-systems PE1 interfaces lt-0/0/0 unit 112 peer-unit 211
set logical-systems PE1 interfaces lt-0/0/0 unit 112 family inet address 10.1.0.1/31
set logical-systems PE1 interfaces lt-0/0/0 unit 112 family iso
set logical-systems PE1 interfaces lt-0/0/0 unit 113 encapsulation vlan
set logical-systems PE1 interfaces lt-0/0/0 unit 113 vlan-id 113
set logical-systems PE1 interfaces lt-0/0/0 unit 113 peer-unit 311
set logical-systems PE1 interfaces lt-0/0/0 unit 113 family inet address 10.1.0.7/31
set logical-systems PE1 interfaces lt-0/0/0 unit 113 family iso
set logical-systems PE1 interfaces ge-0/0/3 unit 16 vlan-id 16
set logical-systems PE1 interfaces ge-0/0/3 unit 16 family inet address 10.0.0.0/31
set logical-systems PE1 interfaces ge-0/0/3 unit 16 family iso
set logical-systems PE1 interfaces lo0 unit 1 family inet address 172.16.0.11/32
set logical-systems PE1 interfaces lo0 unit 1 family iso address 49.0000.0000.0001.00
set logical-systems PE1 protocols isis level 2 wide-metrics-only
set logical-systems PE1 protocols isis interface lt-0/0/0.12
set logical-systems PE1 protocols isis interface ge-0/0/3.16
set logical-systems PE1 protocols isis interface lo0.1
set logical-systems RR1 interfaces lt-0/0/0 unit 42 encapsulation vlan
set logical-systems RR1 interfaces lt-0/0/0 unit 42 vlan-id 24
set logical-systems RR1 interfaces lt-0/0/0 unit 42 peer-unit 24
set logical-systems RR1 interfaces lt-0/0/0 unit 42 family inet address 10.0.0.17/31
set logical-systems RR1 interfaces lt-0/0/0 unit 42 family iso
set logical-systems RR1 interfaces ge-0/0/4 unit 47 vlan-id 47
set logical-systems RR1 interfaces ge-0/0/4 unit 47 family inet address 10.0.0.19/31
set logical-systems RR1 interfaces ge-0/0/4 unit 47 family iso
set logical-systems RR1 interfaces lo0 unit 4 family inet address 172.16.0.201/32
set logical-systems RR1 interfaces lo0 unit 4 family iso address 49.0000.0000.0004.00
set logical-systems RR1 protocols isis interface lt-0/0/0.42
set logical-systems RR1 protocols isis interface ge-0/0/4.47
set logical-systems RR1 protocols isis interface lo0.4
set chassis fpc 0 pic 0 tunnel-services
set chassis fpc 0 lite-mode             
set interfaces ge-0/0/0 unit 0 family inet address 192.168.222.80/24
set interfaces ge-0/0/1 unit 0 family inet address 10.0.0.0/31
set interfaces ge-0/0/1 unit 0 family mpls
set interfaces ge-0/0/2 vlan-tagging
set interfaces ge-0/0/2 encapsulation extended-vlan-bridge
set interfaces ge-0/0/2 unit 1215 vlan-id 1215
set interfaces ge-0/0/3 vlan-tagging
set interfaces ge-0/0/4 vlan-tagging
set interfaces fxp0 unit 0 family inet dhcp vendor-id Juniper-vmx-VM5F9FE3B49D
set interfaces irb unit 1215 family inet address 10.12.15.15/24
set interfaces irb unit 1215 family mpls
set interfaces lo0 unit 0 family inet address 15.15.15.15/32
set interfaces lo0 unit 0 family inet6 address 2001::15/128
set routing-options static route 192.168.20.10/32 next-hop 192.168.222.1
set routing-options autonomous-system 65000
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
