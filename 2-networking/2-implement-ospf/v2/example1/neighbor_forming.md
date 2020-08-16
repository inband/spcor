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
f6:10:c7:fb:bb:83 > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 94: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3929, offset 0, flags [none], proto OSPF (89), length 76)
    192.51.100.201 > 224.0.0.5: OSPFv2, Hello, length 56 [len 44]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3154, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.200 > 192.51.100.201: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.2
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
f6:10:c7:fb:bb:83 > 52:b8:0c:f2:06:ae, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3931, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.201 > 192.51.100.200: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.1
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
f6:10:c7:fb:bb:83 > 52:b8:0c:f2:06:ae, ethertype 802.1Q (0x8100), length 82: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3930, offset 0, flags [none], proto OSPF (89), length 64)
    192.51.100.201 > 192.51.100.200: OSPFv2, Database Description, length 44 [len 32]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS, Opaque], DD Flags [Init, More, Master], MTU: 1500, Sequence: 0x000003da
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 82: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3155, offset 0, flags [none], proto OSPF (89), length 64)
    192.51.100.200 > 192.51.100.201: OSPFv2, Database Description, length 44 [len 32]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS, Opaque], DD Flags [Init, More, Master], MTU: 1500, Sequence: 0x00001c4f
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 122: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3156, offset 0, flags [none], proto OSPF (89), length 104)
    192.51.100.200 > 192.51.100.201: OSPFv2, Database Description, length 84 [len 72]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS, Opaque], DD Flags [More], MTU: 1500, Sequence: 0x000003da
	  Advertising Router 192.51.100.1, seq 0x80000005, age 1091s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.1
	    Options: [External, Demand Circuit]
	  Advertising Router 192.51.100.2, seq 0x8000000a, age 126s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
f6:10:c7:fb:bb:83 > 52:b8:0c:f2:06:ae, ethertype 802.1Q (0x8100), length 102: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3932, offset 0, flags [none], proto OSPF (89), length 84)
    192.51.100.201 > 192.51.100.200: OSPFv2, Database Description, length 64 [len 52]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS, Opaque], DD Flags [Master], MTU: 1500, Sequence: 0x000003db
	  Advertising Router 192.51.100.2, seq 0x8000000c, age 11s, length 28
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 74: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3157, offset 0, flags [none], proto OSPF (89), length 56)
    192.51.100.200 > 192.51.100.201: OSPFv2, LS-Request, length 36
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	  Advertising Router: 192.51.100.2, Router LSA (1), LSA-ID: 192.51.100.2
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 82: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3158, offset 0, flags [none], proto OSPF (89), length 64)
    192.51.100.200 > 192.51.100.201: OSPFv2, Database Description, length 44 [len 32]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS, Opaque], DD Flags [none], MTU: 1500, Sequence: 0x000003db
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
f6:10:c7:fb:bb:83 > 52:b8:0c:f2:06:ae, ethertype 802.1Q (0x8100), length 114: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3933, offset 0, flags [none], proto OSPF (89), length 96)
    192.51.100.201 > 192.51.100.200: OSPFv2, LS-Update, length 76
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0), 1 LSA
	  LSA #1
	  Advertising Router 192.51.100.2, seq 0x8000000c, age 12s, length 28
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
	    Router LSA Options: [none]
	      Stub Network: 192.51.100.2, Mask: 255.255.255.255
		topology default (0), metric 1
	      Stub Network: 192.51.100.200, Mask: 255.255.255.254
		topology default (0), metric 1
f6:10:c7:fb:bb:83 > 52:b8:0c:f2:06:ae, ethertype 802.1Q (0x8100), length 74: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3934, offset 0, flags [none], proto OSPF (89), length 56)
    192.51.100.201 > 192.51.100.200: OSPFv2, LS-Request, length 36
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	  Advertising Router: 192.51.100.1, Router LSA (1), LSA-ID: 192.51.100.1
52:b8:0c:f2:06:ae > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 126: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3159, offset 0, flags [none], proto OSPF (89), length 108)
    192.51.100.200 > 192.51.100.201: OSPFv2, LS-Update, length 88
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0), 1 LSA
	  LSA #1
	  Advertising Router 192.51.100.1, seq 0x80000005, age 1092s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.1
	    Options: [External, Demand Circuit]
	    Router LSA Options: [none]
	      Stub Network: 192.51.100.1, Mask: 255.255.255.255
		topology default (0), metric 1
	      Neighbor Router-ID: 192.51.100.2, Interface Address: 192.51.100.200
		topology default (0), metric 1
	      Stub Network: 192.51.100.200, Mask: 255.255.255.254
		topology default (0), metric 1
f6:10:c7:fb:bb:83 > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 126: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3935, offset 0, flags [none], proto OSPF (89), length 108)
    192.51.100.201 > 224.0.0.5: OSPFv2, LS-Update, length 88
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0), 1 LSA
	  LSA #1
	  Advertising Router 192.51.100.2, seq 0x8000000d, age 1s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
	    Router LSA Options: [none]
	      Stub Network: 192.51.100.2, Mask: 255.255.255.255
		topology default (0), metric 1
	      Neighbor Router-ID: 192.51.100.1, Interface Address: 192.51.100.201
		topology default (0), metric 1
	      Stub Network: 192.51.100.200, Mask: 255.255.255.254
		topology default (0), metric 1
52:b8:0c:f2:06:ae > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3160, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.2
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
52:b8:0c:f2:06:ae > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 102: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3161, offset 0, flags [none], proto OSPF (89), length 84)
    192.51.100.200 > 224.0.0.5: OSPFv2, LS-Ack, length 64
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	  Advertising Router 192.51.100.2, seq 0x8000000c, age 12s, length 28
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
	  Advertising Router 192.51.100.2, seq 0x8000000d, age 1s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.2
	    Options: [External, Demand Circuit]
f6:10:c7:fb:bb:83 > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 82: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3936, offset 0, flags [none], proto OSPF (89), length 64)
    192.51.100.201 > 224.0.0.5: OSPFv2, LS-Ack, length 44
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	  Advertising Router 192.51.100.1, seq 0x80000005, age 1092s, length 40
	    Router LSA (1), LSA-ID: 192.51.100.1
	    Options: [External, Demand Circuit]
f6:10:c7:fb:bb:83 > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3937, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.201 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.1
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
52:b8:0c:f2:06:ae > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3162, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.2
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
```
