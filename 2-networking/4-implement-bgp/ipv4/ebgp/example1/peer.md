# peer

Create an eBGP between CSR10 and CSR1

Configured CSR10 first



```
hostname CSR10
!
interface GigabitEthernet2
 no ip address
 negotiation auto
!
interface GigabitEthernet2.10
 encapsulation dot1Q 10
 ip address 192.51.100.10 255.255.255.254
!
router bgp 64496
 bgp router-id 192.0.2.10
 bgp log-neighbor-changes
 network 192.0.2.0
 neighbor 192.51.100.11 remote-as 64499
 neighbor 192.51.100.11 update-source GigabitEthernet2.10
!
ip route 192.0.2.0 255.255.255.0 Null0
```

```
CSR10#show ip bgp summary    
BGP router identifier 192.0.2.10, local AS number 64496
BGP table version is 2, main routing table version 2
1 network entries using 248 bytes of memory
1 path entries using 120 bytes of memory
1/1 BGP path/bestpath attribute entries using 248 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 616 total bytes of memory
BGP activity 3/2 prefixes, 3/2 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
192.51.100.11   4        64499       0       0        1    0    0 never    Active
CSR10#
```

#### debug ip bgp all

```
*Aug 20 01:46:27.682: BGP: 192.51.100.11 open failed: Connection timed out; remote host not responding
*Aug 20 01:46:27.682: BGP: 192.51.100.11 Active open failed - tcb is not available, open active delayed 9216ms (35000ms max, 60% jitter)
*Aug 20 01:46:27.682: BGP: ses global 192.51.100.11 (0x7FEEC1A7FE10:0) act Reset (Active open failed).
*Aug 20 01:46:27.683: BGP: 192.51.100.11 active went from Active to Idle
*Aug 20 01:46:27.683: BGP: nbr global 192.51.100.11 Active open failed - open timer running
*Aug 20 01:46:27.683: BGP: nbr global 192.51.100.11 Active open failed - opeip bgp summary    
*Aug 20 01:46:36.597: BGP: 192.51.100.11 active went from Idle to Active
*Aug 20 01:46:36.597: BGP: 192.51.100.11 open active, local address 192.51.100.10
*Aug 20 01:46:42.544: BGP: topo global:IPv4 Unicast:base Scanning routing tables
*Aug 20 01:46:42.544: BGP: topo global:VPNv4 Unicast:base Scanning routing tables
*Aug 20 01:46:42.544: BGP: topo LAB_MGMT:VPNv4 Unicast:base Scanning routing tables
*Aug 20 01:46:42.544: BGP: topo global:IPv4 Multicast:base Scanning routing tables

```

Check TCP session detail - note state

```
CSR10#show tcp brief numeric 
TCB       Local Address               Foreign Address             (state)
7FEEC1A7FE10  192.51.100.10.31411        192.51.100.11.179           SYNSENT
CSR10#
```


Check wire

```
root@pve6-lab:~# tcpdump -nneti fwln510i3
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on fwln510i3, link-type EN10MB (Ethernet), capture size 262144 bytes
42:c7:46:4c:5e:8d > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 10, p 0, ethertype ARP, Request who-has 192.51.100.11 tell 192.51.100.10, length 46
42:c7:46:4c:5e:8d > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 10, p 0, ethertype ARP, Request who-has 192.51.100.11 tell 192.51.100.10, length 46
42:c7:46:4c:5e:8d > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 10, p 0, ethertype ARP, Request who-has 192.51.100.11 tell 192.51.100.10, length 46
42:c7:46:4c:5e:8d > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 10, p 0, ethertype ARP, Request who-has 192.51.100.11 tell 192.51.100.10, length 46
42:c7:46:4c:5e:8d > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 10, p 0, ethertype ARP, Request who-has 192.51.100.11 tell 192.51.100.10, length 46
```





