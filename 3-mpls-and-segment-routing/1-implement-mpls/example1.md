# Example 1

AS64499

csr1 ```Lo0 192.51.100.1```

csr2 ```Lo0 192.51.100.2```

csr3 ```Lo0 192.51.100.3```

csr4 ```Lo0 192.51.100.4```


csr1 to csr2 ```vlan12``` ```192.51.100.200/31```

csr1 to csr3 ```vlan13``` ```192.51.100.202/31```

csr2 to csr4 ```vlan24``` ```192.51.100.204/31```

csr3 to csr4 ```vlan34``` ```192.51.100.206/31```

-----------

Baseline setup

igp OSPF - area 0


csr1 route table

```
csr1#show ip route 

Gateway of last resort is 0.0.0.0 to network 0.0.0.0

S*    0.0.0.0/0 is directly connected, Null0
      192.51.100.0/24 is variably subnetted, 10 subnets, 2 masks
C        192.51.100.1/32 is directly connected, Loopback0
O        192.51.100.2/32 
           [110/2] via 192.51.100.201, 00:05:11, GigabitEthernet2.12
O        192.51.100.3/32 
           [110/101] via 192.51.100.203, 00:07:14, GigabitEthernet3.13
O        192.51.100.4/32 
           [110/3] via 192.51.100.201, 00:04:21, GigabitEthernet2.12
C        192.51.100.200/31 is directly connected, GigabitEthernet2.12
L        192.51.100.200/32 is directly connected, GigabitEthernet2.12
C        192.51.100.202/31 is directly connected, GigabitEthernet3.13
L        192.51.100.202/32 is directly connected, GigabitEthernet3.13
O        192.51.100.204/31 
           [110/2] via 192.51.100.201, 00:07:42, GigabitEthernet2.12
O        192.51.100.206/31 
           [110/102] via 192.51.100.201, 00:07:14, GigabitEthernet2.12
```

```
csr1#ping 192.51.100.4 so
csr1#ping 192.51.100.4 source Lo0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.51.100.4, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

csr1#traceroute 192.51.100.4 source Lo0 numeric 
Type escape sequence to abort.
Tracing the route to 192.51.100.4
VRF info: (vrf in name/id, vrf out name/id)
  1 192.51.100.201 2 msec 1 msec 1 msec
  2 192.51.100.205 2 msec *  2 msec

```



```
csr1(config-router)#mpls ldp ?
  autoconfig  Configure LDP automatic configuration

csr1(config-router)#mpls ldp autoconfig
```


```
csr1#show mpls interfaces 
Interface              IP            Tunnel   BGP Static Operational
GigabitEthernet2.12    Yes (ldp)     No       No  No     Yes        
GigabitEthernet3.13    Yes (ldp)     No       No  No     Yes  
```

Show discovery - it can be seen that mpls has chosen Lo0 as the ```Local LDP Identifier``` 

```
csr1#show mpls ldp discovery 
 Local LDP Identifier:
    192.51.100.1:0
    Discovery Sources:
    Interfaces:
        GigabitEthernet2.12 (ldp): xmit
        GigabitEthernet3.13 (ldp): xmit
        
        
csr1#show mpls ldp neighbor 
csr1#
```

As csr1 is the only router configured at this stage for mpls, there is xmit - but no recv.  There are nmo ldp neighbors formed.


What is happening on the wire?

```
root@pve6-lab:~# tcpdump -nnti vmbr0v13 -v
tcpdump: listening on vmbr0v13, link-type EN10MB (Ethernet), capture size 262144 bytes


IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.202.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.1:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.1

```

Key points on above packet.

* it is multicast to **all routers** ```224.0.0.2```
* UDP/646
* TTL 1 (neighbor must be directly connected)
* TOS c0 **InterNetwork Control**

--------

Enable mpls on csr2

```
csr2(config)#router ospf 1
csr2(config-router)#mpls ?
  ldp          routing protocol commands for MPLS LDP
  traffic-eng  routing protocol commands for MPLS Traffic Engineering

csr2(config-router)#mpls ldp autoconfig 
csr2(config-router)#
*Feb  1 02:09:39.593: %LDP-5-NBRCHG: LDP Neighbor 192.51.100.1:0 (1) is UP
csr2(config-router)#
```


```
root@pve6-lab:~# tcpdump -nnti vmbr0v12 port 646 -v
tcpdump: listening on vmbr0v12, link-type EN10MB (Ethernet), capture size 262144 bytes

IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.200.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.1:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.1
IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.200.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.1:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.1
IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.200.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.1:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.1
IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.200.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.1:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.1
IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.201.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.2
IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.200.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.1:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.1
IP (tos 0xc0, ttl 255, id 46323, offset 0, flags [none], proto TCP (6), length 44)
    192.51.100.2.45315 > 192.51.100.1.646: Flags [S], cksum 0xe733 (correct), seq 3019109510, win 4128, options [mss 536], length 0
IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.201.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.2
IP (tos 0xc0, ttl 255, id 61872, offset 0, flags [none], proto TCP (6), length 44)
    192.51.100.1.646 > 192.51.100.2.45315: Flags [S.], cksum 0xed31 (correct), seq 69465549, ack 3019109511, win 4128, options [mss 536], length 0
IP (tos 0xc0, ttl 255, id 46324, offset 0, flags [none], proto TCP (6), length 40)
    192.51.100.2.45315 > 192.51.100.1.646: Flags [.], cksum 0x0153 (correct), ack 1, win 4128, length 0
IP (tos 0xc0, ttl 255, id 46325, offset 0, flags [none], proto TCP (6), length 104)
    192.51.100.2.45315 > 192.51.100.1.646: Flags [.], cksum 0x8a4d (correct), seq 1:65, ack 1, win 4128, length 64
	LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 60
	  Initialization Message (0x0200), length: 50, Message ID: 0x00000001, Flags: [ignore if unknown]
	    Common Session Parameters TLV (0x0500), length: 14, Flags: [ignore and don't forward if unknown]
	      Version: 1, Keepalive: 180s, Flags: [Downstream Unsolicited, Loop Detection Disabled]
	    Unknown TLV (0x0405), length: 4, Flags: [continue processing and don't forward if unknown]
	      0x0000:  8000 0100
	    Unknown TLV (0x0506), length: 1, Flags: [continue processing and don't forward if unknown]
	      0x0000:  80
	    Unknown TLV (0x0508), length: 1, Flags: [continue processing and don't forward if unknown]
	      0x0000:  80
	    Unknown TLV (0x0509), length: 1, Flags: [continue processing and don't forward if unknown]
	      0x0000:  80
	    Unknown TLV (0x050b), length: 1, Flags: [continue processing and don't forward if unknown]
	      0x0000:  80
IP (tos 0xc0, ttl 255, id 61873, offset 0, flags [none], proto TCP (6), length 40)
    192.51.100.1.646 > 192.51.100.2.45315: Flags [.], cksum 0x0153 (correct), ack 65, win 4064, length 0
IP (tos 0xc0, ttl 255, id 61874, offset 0, flags [none], proto TCP (6), length 112)
    192.51.100.1.646 > 192.51.100.2.45315: Flags [.], cksum 0x8836 (correct), seq 1:73, ack 65, win 4064, length 72
	LDP, Label-Space-ID: 192.51.100.1:0, pdu-length: 68
	  Initialization Message (0x0200), length: 50, Message ID: 0x00000001, Flags: [ignore if unknown]
	    Common Session Parameters TLV (0x0500), length: 14, Flags: [ignore and don't forward if unknown]
	      Version: 1, Keepalive: 180s, Flags: [Downstream Unsolicited, Loop Detection Disabled]
	    Unknown TLV (0x0405), length: 4, Flags: [continue processing and don't forward if unknown]
	      0x0000:  8000 0100
	    Unknown TLV (0x0506), length: 1, Flags: [continue processing and don't forward if unknown]
	      0x0000:  80
	    Unknown TLV (0x0508), length: 1, Flags: [continue processing and don't forward if unknown]
	      0x0000:  80
	    Unknown TLV (0x0509), length: 1, Flags: [continue processing and don't forward if unknown]
	      0x0000:  80
	    Unknown TLV (0x050b), length: 1, Flags: [continue processing and don't forward if unknown]
	      0x0000:  80
	  Keepalive Message (0x0201), length: 4, Message ID: 0x00000002, Flags: [ignore if unknown]
IP (tos 0xc0, ttl 255, id 46326, offset 0, flags [none], proto TCP (6), length 342)
    192.51.100.2.45315 > 192.51.100.1.646: Flags [.], cksum 0xc02f (correct), seq 65:367, ack 73, win 4056, length 302
	LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 14
	  Keepalive Message (0x0201), length: 4, Message ID: 0x00000002, Flags: [ignore if unknown]
	LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 280
	  Address Message (0x0300), length: 22, Message ID: 0x00000003, Flags: [ignore if unknown]
	    Address List TLV (0x0101), length: 14, Flags: [ignore and don't forward if unknown]
	      Address Family: IPv4, addresses 192.51.100.201 192.51.100.204 192.51.100.2
	  Label Mapping Message (0x0400), length: 20, Message ID: 0x00000004, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 4, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 0.0.0.0/0
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 3
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x00000005, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.1/32
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 16
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x00000006, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.2/32
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 3
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x00000007, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.3/32
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 17
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x00000008, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.4/32
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 18
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x00000009, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.200/31
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 3
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x0000000a, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.202/31
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 19
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x0000000b, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.204/31
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 3
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x0000000c, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.206/31
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 20
IP (tos 0xc0, ttl 255, id 61875, offset 0, flags [none], proto TCP (6), length 324)
    192.51.100.1.646 > 192.51.100.2.45315: Flags [.], cksum 0xe68a (correct), seq 73:357, ack 367, win 3762, length 284
	LDP, Label-Space-ID: 192.51.100.1:0, pdu-length: 280
	  Address Message (0x0300), length: 22, Message ID: 0x00000003, Flags: [ignore if unknown]
	    Address List TLV (0x0101), length: 14, Flags: [ignore and don't forward if unknown]
	      Address Family: IPv4, addresses 192.51.100.200 192.51.100.202 192.51.100.1
	  Label Mapping Message (0x0400), length: 20, Message ID: 0x00000004, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 4, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 0.0.0.0/0
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 3
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x00000005, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.1/32
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 3
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x00000006, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.2/32
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 16
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x00000007, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.3/32
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 17
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x00000008, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.4/32
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 18
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x00000009, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.200/31
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 3
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x0000000a, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.202/31
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 3
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x0000000b, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.204/31
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 19
	  Label Mapping Message (0x0400), length: 24, Message ID: 0x0000000c, Flags: [ignore if unknown]
	    FEC TLV (0x0100), length: 8, Flags: [ignore and don't forward if unknown]
	      Prefix FEC (0x02): IPv4 prefix 192.51.100.206/31
	    Generic Label TLV (0x0200), length: 4, Flags: [ignore and don't forward if unknown]
	      Label: 20
IP (tos 0xc0, ttl 255, id 46327, offset 0, flags [none], proto TCP (6), length 40)
    192.51.100.2.45315 > 192.51.100.1.646: Flags [.], cksum 0xffe4 (correct), ack 357, win 3772, length 0
IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.200.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.1:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.1
IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.201.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.2
IP (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.200.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.1:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.1

```






