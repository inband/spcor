CSR1 has been configured.

Waiting for CSR2 to be configured.

###### debug
```
CSR1#debug ip ospf 1 hello 
OSPF hello debugging is on for process 1
*Aug 16 07:22:55.563: OSPF-1 HELLO Gi2.12: Send hello to 224.0.0.5 area 0 from 192.51.100.200
*Aug 16 07:23:05.328: OSPF-1 HELLO Gi2.12: Send hello to 224.0.0.5 area 0 from 192.51.100.200
```

###### tcpdump
```
tcpdump -nnei fwln510i1 -v
17:19:33.132129 52:b8:0c:f2:06:ae > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 94: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 2144, offset 0, flags [none], proto OSPF (89), length 76)
    192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 56 [len 44]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]

17:19:42.738356 52:b8:0c:f2:06:ae > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 94: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 2145, offset 0, flags [none], proto OSPF (89), length 76)
    192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 56 [len 44]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]

```
