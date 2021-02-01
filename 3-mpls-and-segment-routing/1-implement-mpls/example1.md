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
