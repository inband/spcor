# neighbor forming

###### debug CSR1
```
CSR1#debug ip ospf 1 adj 
OSPF adjacency debugging is on for process 1
CSR1#
*Aug 16 07:30:13.950: OSPF-1 ADJ   Gi2.12: Cannot see ourself in hello from 192.51.100.2, state INIT
*Aug 16 07:30:13.954: OSPF-1 ADJ   Gi2.12: 2 Way Communication to 192.51.100.2, state 2WAY
*Aug 16 07:30:13.954: OSPF-1 ADJ   Gi2.12: Nbr 192.51.100.2: Prepare dbase exchange
*Aug 16 07:30:13.954: OSPF-1 ADJ   Gi2.12: Send DBD to 192.51.100.2 seq 0x450 opt 0x52 flag 0x7 len 32
*Aug 16 07:30:13.954: OSPF-1 ADJ   Gi2.12: Rcv DBD from 192.51.100.2 seq 0x2214 opt 0x52 flag 0x7 len 32  mtu 1500 state EXSTART
*Aug 16 07:30:13.954: OSPF-1 ADJ   Gi2.12: NBR Negotiation Done. We are the SLAVE
*Aug 16 07:30:13.954: OSPF-1 ADJ   Gi2.12: Nbr 192.51.100.2: Summary list built, size 2
*Aug 16 07:30:13.954: OSPF-1 ADJ   Gi2.12: Send DBD to 192.51.100.2 seq 0x2214 opt 0x52 flag 0x2 len 72
*Aug 16 07:30:13.955: OSPF-1 ADJ   Gi2.12: Rcv DBD from 192.51.100.2 seq 0x2215 opt 0x52 flag 0x1 len 72  mtu 1500 state EXCHANGE
*Aug 16 07:30:13.955: OSPF-1 ADJ   Gi2.12: Exchange Done with 192.51.100.2
*Aug 16 07:30:13.955: OSPF-1 ADJ   Gi2.12: Send LS REQ to 192.51.100.2 length 36 LSA count 1
*Aug 16 07:30:13.955: OSPF-1 ADJ   Gi2.12: Send DBD to 192.51.100.2 seq 0x2215 opt 0x52 flag 0x0 len 32
*Aug 16 07:30:13.956: OSPF-1 ADJ   Gi2.12: Rcv LS UPD from 192.51.100.2 length 76 LSA count 1
*Aug 16 07:30:13.956: OSPF-1 ADJ   Gi2.12: Synchronized with 192.51.100.2, state FULL
*Aug 16 07:30:13.956: %OSPF-5-ADJCHG: Process 1, Nbr 192.51.100.2 on GigabitEthernet2.12 from LOADING to FULL, Loading Done

```

###### tcpdump


```
52:b8:0c:f2:06:ae > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 2389, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.2
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
f6:10:c7:fb:bb:83 > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 94: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 1357, offset 0, flags [none], proto OSPF (89), length 76)
    192.51.100.201 > 224.0.0.5: OSPFv2, Hello, length 56 [len 44]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 2390, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.200 > 192.51.100.201: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.2
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
f6:10:c7:fb:bb:83 > 52:b8:0c:f2:06:ae, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 1359, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.201 > 192.51.100.200: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.1
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
f6:10:c7:fb:bb:83 > 52:b8:0c:f2:06:ae, ethertype 802.1Q (0x8100), length 82: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 1358, offset 0, flags [none], proto OSPF (89), length 64)
    192.51.100.201 > 192.51.100.200: OSPFv2, Database Description, length 44 [len 32]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS, Opaque], DD Flags [Init, More, Master], MTU: 1500, Sequence: 0x0000044e
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 82: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 2391, offset 0, flags [none], proto OSPF (89), length 64)
    192.51.100.200 > 192.51.100.201: OSPFv2, Database Description, length 44 [len 32]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS, Opaque], DD Flags [Init, More, Master], MTU: 1500, Sequence: 0x000024c1
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 122: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 2392, offset 0, flags [none], proto OSPF (89), length 104)
    192.51.100.200 > 192.51.100.201: OSPFv2, Database Description, length 84 [len 72]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS, Opaque], DD Flags [More], MTU: 1500, Sequence: 0x0000044e
	  Advertising Router 192.51.100.1, seq 0x80000003, age 56s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.1
	    Options: [External, Demand Circuit]
	  Advertising Router 192.51.100.2, seq 0x80000003, age 57s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
f6:10:c7:fb:bb:83 > 52:b8:0c:f2:06:ae, ethertype 802.1Q (0x8100), length 122: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 1360, offset 0, flags [none], proto OSPF (89), length 104)
    192.51.100.201 > 192.51.100.200: OSPFv2, Database Description, length 84 [len 72]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS, Opaque], DD Flags [Master], MTU: 1500, Sequence: 0x0000044f
	  Advertising Router 192.51.100.1, seq 0x80000003, age 57s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.1
	    Options: [External, Demand Circuit]
	  Advertising Router 192.51.100.2, seq 0x80000004, age 27s, length 28
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 74: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 2393, offset 0, flags [none], proto OSPF (89), length 56)
    192.51.100.200 > 192.51.100.201: OSPFv2, LS-Request, length 36
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	  Advertising Router: 192.51.100.2, Router LSA (1), LSA-ID: 192.51.100.2
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 82: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 2394, offset 0, flags [none], proto OSPF (89), length 64)
    192.51.100.200 > 192.51.100.201: OSPFv2, Database Description, length 44 [len 32]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS, Opaque], DD Flags [none], MTU: 1500, Sequence: 0x0000044f
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
f6:10:c7:fb:bb:83 > 52:b8:0c:f2:06:ae, ethertype 802.1Q (0x8100), length 114: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 1361, offset 0, flags [none], proto OSPF (89), length 96)
    192.51.100.201 > 192.51.100.200: OSPFv2, LS-Update, length 76
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0), 1 LSA
	  LSA #1
	  Advertising Router 192.51.100.2, seq 0x80000004, age 28s, length 28
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
	    Router LSA Options: [none]
	      Stub Network: 192.51.100.2, Mask: 255.255.255.255
		topology default (0), metric 1
	      Stub Network: 192.51.100.200, Mask: 255.255.255.254
		topology default (0), metric 1
f6:10:c7:fb:bb:83 > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 126: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 1362, offset 0, flags [none], proto OSPF (89), length 108)
    192.51.100.201 > 224.0.0.5: OSPFv2, LS-Update, length 88
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0), 1 LSA
	  LSA #1
	  Advertising Router 192.51.100.2, seq 0x80000005, age 1s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
	    Router LSA Options: [none]
	      Stub Network: 192.51.100.2, Mask: 255.255.255.255
		topology default (0), metric 1
	      Neighbor Router-ID: 192.51.100.1, Interface Address: 192.51.100.201
		topology default (0), metric 1
	      Stub Network: 192.51.100.200, Mask: 255.255.255.254
		topology default (0), metric 1
52:b8:0c:f2:06:ae > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 102: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 2396, offset 0, flags [none], proto OSPF (89), length 84)
    192.51.100.200 > 224.0.0.5: OSPFv2, LS-Ack, length 64
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	  Advertising Router 192.51.100.2, seq 0x80000004, age 28s, length 28
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
	  Advertising Router 192.51.100.2, seq 0x80000005, age 1s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
52:b8:0c:f2:06:ae > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 2397, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.2
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
f6:10:c7:fb:bb:83 > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 1363, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.201 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.1
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]

```


###### verfiy

```
CSR1#show ip ospf 1 neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.51.100.2      0   FULL/  -        00:00:35    192.51.100.201  GigabitEthernet2.12

CSR2#show ip route                                                                                          
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

      192.51.100.0/24 is variably subnetted, 4 subnets, 2 masks
O        192.51.100.1/32 
           [110/2] via 192.51.100.200, 00:05:28, GigabitEthernet2.12
C        192.51.100.2/32 is directly connected, Loopback0
C        192.51.100.200/31 is directly connected, GigabitEthernet2.12
L        192.51.100.201/32 is directly connected, GigabitEthernet2.12

CSR2#ping 192.51.100.1 source loopback 0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.51.100.1, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.2 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

CSR2#traceroute 192.51.100.1 source loopback 0 numeric 
Type escape sequence to abort.
Tracing the route to 192.51.100.1
VRF info: (vrf in name/id, vrf out name/id)
  1 192.51.100.200 1 msec *  1 msec

```
