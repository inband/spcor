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


Check wire (CSR1)

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


Configue CSR1

```
hostname CSR1
!
interface GigabitEthernet4
 no ip address
 negotiation auto
!
interface GigabitEthernet4.10
 encapsulation dot1Q 10
 ip address 192.51.100.11 255.255.255.254
!
```

Now what is happening on wire (CSR1)

```
42:c7:46:4c:5e:8d > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 10, p 0, ethertype ARP, Request who-has 192.51.100.11 tell 192.51.100.10, length 46
42:c7:46:4c:5e:8d > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 10, p 0, ethertype ARP, Request who-has 192.51.100.11 tell 192.51.100.10, length 46
32:14:f3:7d:63:4d > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 10, p 0, ethertype ARP, Reply 192.51.100.11 is-at 32:14:f3:7d:63:4d, length 46
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 62: vlan 10, p 0, ethertype IPv4, 192.51.100.10.42182 > 192.51.100.11.179: Flags [S], seq 1657406469, win 16384, options [mss 1460], length 0
32:14:f3:7d:63:4d > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 10, p 0, ethertype ARP, Request who-has 192.51.100.10 tell 192.51.100.11, length 46
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 64: vlan 10, p 0, ethertype ARP, Reply 192.51.100.10 is-at 42:c7:46:4c:5e:8d, length 46
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 62: vlan 10, p 0, ethertype IPv4, 192.51.100.10.42182 > 192.51.100.11.179: Flags [S], seq 1657406469, win 16384, options [mss 1460], length 0
32:14:f3:7d:63:4d > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 58: vlan 10, p 0, ethertype IPv4, 192.51.100.11.179 > 192.51.100.10.42182: Flags [R.], seq 0, ack 1657406470, win 0, length 0
```

#### debug CSR10

Note **Connection refused by remote host**

```
*Aug 20 02:04:19.683: BGP: 192.51.100.11 active went from Idle to Active
*Aug 20 02:04:19.683: BGP: 192.51.100.11 open active, local address 192.51.100.10
*Aug 20 02:04:19.685: BGP: 192.51.100.11 open failed: Connection refused by remote host
```




Now add eBGP config to CSR1

```
router bgp 64499
 bgp log-neighbor-changes
 network 192.51.100.0
 redistribute connected
 neighbor 192.51.100.2 remote-as 64499
 neighbor 192.51.100.2 update-source Loopback0
 neighbor 192.51.100.2 prefix-list PL_IBGP_OUT out
 neighbor 192.51.100.10 remote-as 64496
 neighbor 192.51.100.10 update-source GigabitEthernet4.10

```


CSR10 TCP state

```
CSR10#show tcp brief numeric          
TCB       Local Address               Foreign Address             (state)
7FEF11374488  192.51.100.10.22138        192.51.100.11.179           ESTAB
```



#### debug CSR10

```
*Aug 20 02:06:44.102: BGP: ses global 192.51.100.11 (0x7FEEC1066D58:0) act Reset (Active open failed).
*Aug 20 02:06:44.102: BGP: 192.51.100.11 active went from Active to Idle
*Aug 20 02:06:44.102: BGP: nbr global 192.51.100.11 Active open failed - open timer running
*Aug 20 02:06:44.102: BGP: nbr global 192.51.100.11 Active open failed - open timer running
*Aug 20 02:06:58.437: BGP: 192.51.100.11 active went from Idle to Active
*Aug 20 02:06:58.437: BGP: 192.51.100.11 open active, local address 192.51.100.10
*Aug 20 02:06:58.440: BGP: ses global 192.51.100.11 (0x7FEEC1066D58:0) act Adding topology IPv4 Unicast:base
*Aug 20 02:06:58.440: BGP: ses global 192.51.100.11 (0x7FEEC1066D58:0) act Send OPEN
*Aug 20 02:06:58.440: BGP: ses global 192.51.100.11 (0x7FEEC1066D58:0) act Building Enhanced Refresh capability
*Aug 20 02:06:58.440: BGP: 192.51.100.11 active went from Active to OpenSent
*Aug 20 02:06:58.440: BGP: 192.51.100.11 active sending OPEN, version 4, my as: 64496, holdtime 180 seconds, ID C000020A
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active rcv message type 1, length (excl. header) 38
*Aug 20 02:06:58.442: BGP: ses global 192.51.100.11 (0x7FEEC1066D58:0) act Receive OPEN
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active rcv OPEN, version 4, holdtime 180 seconds
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active rcv OPEN w/ OPTION parameter len: 28
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active rcvd OPEN w/ optional parameter type 2 (Capability) len 6
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active OPEN has CAPABILITY code: 1, length 4
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active OPEN has MP_EXT CAP for afi/safi: 1/1
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active rcvd OPEN w/ optional parameter type 2 (Capability) len 2
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active OPEN has CAPABILITY code: 128, length 0
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active OPEN has ROUTE-REFRESH capability(old) for all address-families
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active rcvd OPEN w/ optional parameter type 2 (Capability) len 2
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active OPEN has CAPABILITY code: 2, length 0
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active OPEN has ROUTE-REFRESH capability(new) for all address-families
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active rcvd OPEN w/ optional parameter type 2 (Capability) len 2
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active OPEN has CAPABILITY code: 70, length 0
*Aug 20 02:06:58.442: BGP: ses global 192.51.100.11 (0x7FEEC1066D58:0) act Enhanced Refresh cap received in open message
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active rcvd OPEN w/ optional parameter type 2 (Capability) len 6
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active OPEN has CAPABILITY code: 65, length 4
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active OPEN has 4-byte ASN CAP for: 64499
*Aug 20 02:06:58.442: BGP: nbr global 192.51.100.11 neighbor does not have IPv4 MDT topology activated
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active rcvd OPEN w/ remote AS 64499, 4-byte remote AS 64499
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active went from OpenSent to OpenConfirm
*Aug 20 02:06:58.442: BGP: 192.51.100.11 active went from OpenConfirm to Established
*Aug 20 02:06:58.442: BGP: ses global 192.51.100.11 (0x7FEEC1066D58:1) act Assigned ID
*Aug 20 02:06:58.442: BGP: ses global 192.51.100.11 (0x7FEEC1066D58:1) Up
*Aug 20 02:06:58.442: %BGP-5-ADJCHANGE: neighbor 192.51.100.11 Up 
```


what happened on wire (CSR1)

```
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 62: vlan 10, p 0, ethertype IPv4, 192.51.100.10.22138 > 192.51.100.11.179: Flags [S], seq 3612284666, win 16384, options [mss 1460], length 0
32:14:f3:7d:63:4d > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 62: vlan 10, p 0, ethertype IPv4, 192.51.100.11.179 > 192.51.100.10.22138: Flags [S.], seq 414735957, ack 3612284667, win 16384, options [mss 1460], length 0
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 58: vlan 10, p 0, ethertype IPv4, 192.51.100.10.22138 > 192.51.100.11.179: Flags [.], ack 1, win 16384, length 0
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 115: vlan 10, p 0, ethertype IPv4, 192.51.100.10.22138 > 192.51.100.11.179: Flags [P.], seq 1:58, ack 1, win 16384, length 57: BGP
32:14:f3:7d:63:4d > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 58: vlan 10, p 0, ethertype IPv4, 192.51.100.11.179 > 192.51.100.10.22138: Flags [.], ack 58, win 16327, length 0
32:14:f3:7d:63:4d > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 115: vlan 10, p 0, ethertype IPv4, 192.51.100.11.179 > 192.51.100.10.22138: Flags [P.], seq 1:58, ack 58, win 16327, length 57: BGP
32:14:f3:7d:63:4d > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 77: vlan 10, p 0, ethertype IPv4, 192.51.100.11.179 > 192.51.100.10.22138: Flags [P.], seq 58:77, ack 58, win 16327, length 19: BGP
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 58: vlan 10, p 0, ethertype IPv4, 192.51.100.10.22138 > 192.51.100.11.179: Flags [.], ack 77, win 16308, length 0
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 77: vlan 10, p 0, ethertype IPv4, 192.51.100.10.22138 > 192.51.100.11.179: Flags [P.], seq 58:77, ack 77, win 16308, length 19: BGP
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 77: vlan 10, p 0, ethertype IPv4, 192.51.100.10.22138 > 192.51.100.11.179: Flags [P.], seq 77:96, ack 77, win 16308, length 19: BGP
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 135: vlan 10, p 0, ethertype IPv4, 192.51.100.10.22138 > 192.51.100.11.179: Flags [P.], seq 96:173, ack 77, win 16308, length 77: BGP
32:14:f3:7d:63:4d > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 77: vlan 10, p 0, ethertype IPv4, 192.51.100.11.179 > 192.51.100.10.22138: Flags [P.], seq 77:96, ack 77, win 16308, length 19: BGP
32:14:f3:7d:63:4d > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 210: vlan 10, p 0, ethertype IPv4, 192.51.100.11.179 > 192.51.100.10.22138: Flags [P.], seq 96:248, ack 77, win 16308, length 152: BGP
32:14:f3:7d:63:4d > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 58: vlan 10, p 0, ethertype IPv4, 192.51.100.11.179 > 192.51.100.10.22138: Flags [.], ack 173, win 16212, length 0
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 58: vlan 10, p 0, ethertype IPv4, 192.51.100.10.22138 > 192.51.100.11.179: Flags [.], ack 248, wi

```



