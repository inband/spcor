# ospf hello 

Also used as keepalive.

Neighbor table as reference.

```
CSR1#show ip ospf 1 neighbor GigabitEthernet 2.12        

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.51.100.2      0   FULL/  -        00:00:37    192.51.100.201  GigabitEthernet2.12
```


Monitoring the link between CSR1 and CSR2.  Note ospf is in steady state.

```
root@pve6-lab:~# tcpdump -nnei fwln510i1 multicast -v
tcpdump: listening on fwln510i1, link-type EN10MB (Ethernet), capture size 262144 bytes

11:34:34.095276 f6:10:c7:fb:bb:83 > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 57558, offset 0, flags [none], proto OSPF (89), length 80)
    192.51.100.201 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
	Router-ID 192.51.100.2, Backbone Area, Authentication Type: none (0)
	Options [External, LLS]
	  Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
	  Neighbor List:
	    192.51.100.1
	  LLS: checksum: 0xfff6, length: 3
	    Extended Options (1), length: 4
	      Options: 0x00000001 [LSDB resync]

11:34:35.779028 52:b8:0c:f2:06:ae > 01:00:5e:00:00:05, ethertype 802.1Q (0x8100), length 98: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 58840, offset 0, flags [none], proto OSPF (89), length 80)
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

Observations:
* hello timer is 10s
* dead timer is 40s (4 x hello timer)
* src IP address is the IP of the interface 
* dst IP is multicast IP **224.0.0.5**
* dst MAC is multicast MAC **01:00:5e:00:00:05**
* **router-id** information is transported detail
* no Authentication
* a neighbor list identifying adjacency neighbor **router id**


Turn of verbose filter and now see bidirectional hellos (every 10s in each direction)

```
root@pve6-lab:~# tcpdump -nni fwln510i1 multicast
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on fwln510i1, link-type EN10MB (Ethernet), capture size 262144 bytes
11:38:06.260351 IP 192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 60
11:38:11.881490 IP 192.51.100.201 > 224.0.0.5: OSPFv2, Hello, length 60
11:38:15.417429 IP 192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 60
11:38:21.593791 IP 192.51.100.201 > 224.0.0.5: OSPFv2, Hello, length 60
11:38:24.933169 IP 192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 60
11:38:31.008426 IP 192.51.100.201 > 224.0.0.5: OSPFv2, Hello, length 60
11:38:33.983785 IP 192.51.100.200 > 224.0.0.5: OSPFv2, Hello, length 60
```
