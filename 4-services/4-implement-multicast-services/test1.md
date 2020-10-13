# Test 1 - getting a handle on Multicast

No expierience with Multicast

Started out watching INE Multicast - bad idea. I'll watch this after I've read(Cisco Press/rfc's and labbed).  

Found Beau Williamson's "Fundamentals of IP Multicast" Live Lesson.  This is a much more gentler introduction.  

So far my initial understanding of multicast:

* Multicast is used for 'targeted' broadcasts that traverse routers (broadcast domain)
* or a special sub-set for link-local that basically operate like broadcast ```224.0.0.0/24``` ie OSPF, HSRP, IGMPv3
* The use case I like to think of is bradcast television - IPTV
* like unicast there is a SRC IP
* unlike unicast there is NOT a DST IP rather a "receiver"
* the "receiver" can be part of a GROUP
* the "receiver" IP is in multicast allocation of IPs
* the "receiver" tunes into the "src"
* the "receiver" knows about the "src" and this is used to build/signal a source tree from "receiver" to "src"(sender)
* setup is **BACKWARDS**
* the last hop router (the router directly connected to the "receiver") accepts an **IGMP Join** from "receiver"
* the **IGMP Join** is a host to router message
* the last hop router looks up the "SRC IP" in RIB(this will be used for RPF verification) and builds a MROUTE table.
* then last hop router sends **PIM Join** out toward "SRC IP"
* **PIM Join** is router to router message
* next router looks up "SRC IP" in RIB and build MROUTE table
* and so on
* until we get to FHR - first hop router (the router directly connected to the source)
* once FHR is setup the path is in effect signalled and "multicast" flow can occur.


Anyway, I need a break from theory so time to play around and get a proper feel for it.

I'm going to setup IGMPv3/SSM

Using AS64511 (```csr5```, ```csr6```,  ```csr8```)

I have unicast routing in the GLOBAL routing table.

I'll make ```csr5``` the source and ```csr8``` the receiver.


```csr7```can be added later to test behaviour in ECMP situation ```csr5``` to ```csr8``` over OSPFv2.  At the moment the ECMP is NOT in effect.


So Beau says:

Enable on **every** router and **every** interface

Globally

```
ip multicast-routing distributed 
```

Interface

```
ip pim sparse-mode
ip igmp version 3
```


Now before I added ```ip igmp version 3``` on ```csr5```

Looking at wire 

```
root@pve6-lab:~# tcpdump -nntei vmbr0v56 'not udp && not proto ospf && not port 179 && not port 646 && not mpls'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on vmbr0v56, link-type EN10MB (Ethernet), capture size 262144 bytes
ea:8d:e3:78:ba:07 > 01:00:5e:00:01:28, ethertype 802.1Q (0x8100), length 50: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.1.40: igmp v2 report 224.0.1.40
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:0d, ethertype 802.1Q (0x8100), length 76: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.0.13: PIMv2, Hello, length 38
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:01, ethertype 802.1Q (0x8100), length 50: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.0.1: igmp query v2 [max resp time 25]
ea:8d:e3:78:ba:07 > 01:00:5e:00:01:28, ethertype 802.1Q (0x8100), length 50: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.1.40: igmp v2 report 224.0.1.40
```

* ```csr5``` is using ```172.31.0.200``` which is interface IP.
* IGMPv2 is default
* ICMPv2 report ```224.0.1.40```
* ICMPv2 query ```224.0.0.1``` - this is to all hosts on directly connected.
* PIM looks to be trying to discover over PIM on local-link ```224.0.0.13```



Enable ```ip igmp version 3```

```
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:16, ethertype 802.1Q (0x8100), length 58: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.0.22: igmp v3 report, 1 group record(s)
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:01, ethertype 802.1Q (0x8100), length 54: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.0.1: igmp query v3 [max resp time 2.5s]
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:16, ethertype 802.1Q (0x8100), length 58: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.0.22: igmp v3 report, 1 group record(s)
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:16, ethertype 802.1Q (0x8100), length 58: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.0.22: igmp v3 report, 1 group record(s)

ea:8d:e3:78:ba:07 > 01:00:5e:00:00:0d, ethertype 802.1Q (0x8100), length 76: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.0.13: PIMv2, Hello, length 38
```


* PIM remains ```224.0.0.13``` (same as above)
* IGMPv3 query ```224.0.0.1``` - all hosts
* IGMPv3 report ```224.0.0.22``` Link-local IGMPv3


Now enable on other end of link ```csr6```


```
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:0d, ethertype 802.1Q (0x8100), length 76: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.0.13: PIMv2, Hello, length 38
da:41:34:42:c8:10 > 01:00:5e:00:00:0d, ethertype 802.1Q (0x8100), length 76: vlan 56, p 0, ethertype IPv4, 172.31.0.201 > 224.0.0.13: PIMv2, Hello, length 38
da:41:34:42:c8:10 > 01:00:5e:00:00:16, ethertype 802.1Q (0x8100), length 58: vlan 56, p 0, ethertype IPv4, 172.31.0.201 > 224.0.0.22: igmp v3 report, 1 group record(s)
da:41:34:42:c8:10 > 01:00:5e:00:00:01, ethertype 802.1Q (0x8100), length 54: vlan 56, p 0, ethertype IPv4, 172.31.0.201 > 224.0.0.1: igmp query v3 [max resp time 2.5s]
da:41:34:42:c8:10 > 01:00:5e:00:00:16, ethertype 802.1Q (0x8100), length 58: vlan 56, p 0, ethertype IPv4, 172.31.0.201 > 224.0.0.22: igmp v3 report, 1 group record(s)
da:41:34:42:c8:10 > 01:00:5e:00:00:16, ethertype 802.1Q (0x8100), length 58: vlan 56, p 0, ethertype IPv4, 172.31.0.201 > 224.0.0.22: igmp v3 report, 1 group record(s)
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:16, ethertype 802.1Q (0x8100), length 58: vlan 56, p 0, ethertype IPv4, 172.31.0.200 > 224.0.0.22: igmp v3 report, 1 group record(s)
```

```csr5``` appears to be DR

```
CSR5#show ip pim neighbor 
PIM Neighbor Table
Mode: B - Bidir Capable, DR - Designated Router, N - Default DR Priority,
      P - Proxy Capable, S - State Refresh Capable, G - GenID Capable,
      L - DR Load-balancing Capable
Neighbor          Interface                Uptime/Expires    Ver   DR
Address                                                            Prio/Mode
172.31.0.201      GigabitEthernet2.56      00:02:13/00:01:28 v2    1 / DR S P G
```

```csr6```

```
CSR6(config-subif)#do show ip pim neighbor 
PIM Neighbor Table
Mode: B - Bidir Capable, DR - Designated Router, N - Default DR Priority,
      P - Proxy Capable, S - State Refresh Capable, G - GenID Capable,
      L - DR Load-balancing Capable
Neighbor          Interface                Uptime/Expires    Ver   DR
Address                                                            Prio/Mode
172.31.0.200      GigabitEthernet2.56      00:05:35/00:01:33 v2    1 / S P G
```


Lets example these packets a little more.

PIM

```
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:0d, ethertype 802.1Q (0x8100), length 76: vlan 56, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 32683, offset 0, flags [none], proto PIM (103), length 58)
    172.31.0.200 > 224.0.0.13: PIMv2, length 38
	Hello, cksum 0xc08e (correct)
	  Hold Time Option (1), length 2, Value: 1m45s
	  Generation ID Option (20), length 4, Value: 0x3f65e069
	  DR Priority Option (19), length 4, Value: 1
	  State Refresh Capability Option (21), length 4, Value: v1
	  Unknown Option (65004), length 0, Value: 

```


IGMPv3 query

```
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:01, ethertype 802.1Q (0x8100), length 54: vlan 56, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 32709, offset 0, flags [none], proto IGMP (2), length 36, options (RA))
    172.31.0.200 > 224.0.0.1: igmp query v3


```


IGMPv3 report

```
ea:8d:e3:78:ba:07 > 01:00:5e:00:00:16, ethertype 802.1Q (0x8100), length 58: vlan 56, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 32712, offset 0, flags [none], proto IGMP (2), length 40, options (RA))
    172.31.0.200 > 224.0.0.22: igmp v3 report, 1 group record(s) [gaddr 224.0.1.40 is_ex, 0 source(s)]
da:41:34:42:c8:10 > 01:00:5e:00:00:16, ethertype 802.1Q (0x8100), length 58: vlan 56, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 12922, offset 0, flags [none], proto IGMP (2), length 40, options (RA))
    172.31.0.201 > 224.0.0.22: igmp v3 report, 1 group record(s) [gaddr 224.0.1.40 is_ex, 0 source(s)]

```



* each of these protocols have tos 0xc0 which is cs6 Internet control


MROUTE

```
CSR5#show ip mroute 
IP Multicast Routing Table
Flags: D - Dense, S - Sparse, B - Bidir Group, s - SSM Group, C - Connected,
       L - Local, P - Pruned, R - RP-bit set, F - Register flag,
       T - SPT-bit set, J - Join SPT, M - MSDP created entry, E - Extranet,
       X - Proxy Join Timer Running, A - Candidate for MSDP Advertisement,
       U - URD, I - Received Source Specific Host Report, 
       Z - Multicast Tunnel, z - MDT-data group sender, 
       Y - Joined MDT-data group, y - Sending to MDT-data group, 
       G - Received BGP C-Mroute, g - Sent BGP C-Mroute, 
       N - Received BGP Shared-Tree Prune, n - BGP C-Mroute suppressed, 
       Q - Received BGP S-A Route, q - Sent BGP S-A Route, 
       V - RD & Vector, v - Vector, p - PIM Joins on route, 
       x - VxLAN group, c - PFP-SA cache created entry
Outgoing interface flags: H - Hardware switched, A - Assert winner, p - PIM Join
 Timers: Uptime/Expires
 Interface state: Interface, Next-Hop or VCD, State/Mode

(*, 224.0.1.40), 01:13:24/00:02:35, RP 0.0.0.0, flags: DPL
  Incoming interface: Null, RPF nbr 0.0.0.0
  Outgoing interface list: Null

```

Hmmm not sure if this is something left over from IGMPv2


-------------------------------------

Ok setup on remaining link

```
CSR5#show ip pim neighbor 
PIM Neighbor Table
Mode: B - Bidir Capable, DR - Designated Router, N - Default DR Priority,
      P - Proxy Capable, S - State Refresh Capable, G - GenID Capable,
      L - DR Load-balancing Capable
Neighbor          Interface                Uptime/Expires    Ver   DR
Address                                                            Prio/Mode
172.31.0.201      GigabitEthernet2.56      00:28:15/00:01:30 v2    1 / DR S P G
```

```
CSR6#show ip pim neighbor 
PIM Neighbor Table
Mode: B - Bidir Capable, DR - Designated Router, N - Default DR Priority,
      P - Proxy Capable, S - State Refresh Capable, G - GenID Capable,
      L - DR Load-balancing Capable
Neighbor          Interface                Uptime/Expires    Ver   DR
Address                                                            Prio/Mode
172.31.0.200      GigabitEthernet2.56      00:27:00/00:01:18 v2    1 / S P G
172.31.0.205      GigabitEthernet3.68      00:01:22/00:01:21 v2    1 / DR S P G
```

```
CSR8#show ip pim neighbor 
PIM Neighbor Table
Mode: B - Bidir Capable, DR - Designated Router, N - Default DR Priority,
      P - Proxy Capable, S - State Refresh Capable, G - GenID Capable,
      L - DR Load-balancing Capable
Neighbor          Interface                Uptime/Expires    Ver   DR
Address                                                            Prio/Mode
172.31.0.204      GigabitEthernet2.68      00:01:15/00:01:29 v2    1 / S P G

```

Observation

* each link has DR


