# OSPF v2


Below is default behaviour.  ```CSR1``` is sending hello via multicast over ```vlan 12```.  Observations:

* no CoS
* DSCP 0xc0 - this is **cs6 Internetwork Control**



```
tcpdump: listening on vmbr0v12, link-type EN10MB (Ethernet), capture size 262144 bytes
12:11:38.579935 62:3b:76:19:58:fa > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 106: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 12153, offset 0, flags [none], proto OSPF (89), length 88)
    192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 68 [len 44]
	Router-ID 192.51.100.1, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  LLS: checksum: 0x7fd1, length: 6
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]
	    Unknown TLV (32768), length: 8

```
