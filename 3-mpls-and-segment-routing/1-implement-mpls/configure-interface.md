# mpls ip


CSR2

```
CSR2#debug mpls events 
MPLS events debugging is on

CSR2#terminal monitor 


CSR2(config)#mpls ldp router-id lo0

CSR2(config)#int GigabitEthernet3.24
CSR2(config-subif)#mpls ip

*Aug 22 08:31:33.939: mpls: Add mpls app; GigabitEthernet3.24 
*Aug 22 08:31:33.939: LDP SB: i/f status change; GigabitEther       

CSR2(config-subif)#int GigabitEthernet2.12
CSR2(config-subif)#mpls ip                

*Aug 22 08:31:46.057: mpls: Add mpls app; GigabitEthernet2.12 
*Aug 22 08:31:46.057: LDP SB: i/f status change; GigabitEthernet2.12
```

Local ldp **bindings** 

```
CSR2#show mpls ldp bindings 
  lib entry: 192.51.100.0/24, rev 2
	local binding:  label: imp-null
  lib entry: 192.51.100.1/32, rev 4
	local binding:  label: 16
  lib entry: 192.51.100.2/32, rev 6
	local binding:  label: imp-null
  lib entry: 192.51.100.3/32, rev 8
	local binding:  label: 17
  lib entry: 192.51.100.4/32, rev 10
	local binding:  label: 18
  lib entry: 192.51.100.200/31, rev 12
	local binding:  label: imp-null
  lib entry: 192.51.100.202/31, rev 14
	local binding:  label: 19
  lib entry: 192.51.100.204/31, rev 16
	local binding:  label: imp-null
  lib entry: 192.51.100.206/31, rev 18
	local binding:  label: 20

```

LFIB
```
CSR2#show mpls forwarding-table 
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop    
Label      Label      or Tunnel Id     Switched      interface              
16         No Label   192.51.100.1/32  0             Gi2.12     192.51.100.200
17         No Label   192.51.100.3/32  0             Gi2.12     192.51.100.200
           No Label   192.51.100.3/32  0             Gi3.24     192.51.100.205
18         No Label   192.51.100.4/32  0             Gi3.24     192.51.100.205
19         No Label   192.51.100.202/31   \
                                       0             Gi2.12     192.51.100.200
20         No Label   192.51.100.206/31   \
                                       0             Gi3.24     192.51.100.205
```


**ldp** is transmitting


```
CSR2#show mpls ldp discovery 
 Local LDP Identifier:
    192.51.100.2:0
    Discovery Sources:
    Interfaces:
	GigabitEthernet2.12 (ldp): xmit
	GigabitEthernet3.24 (ldp): xmit
```

No neighbors as CSR1, CSR3 and CSR4 have not had ```mpls ip``` added to interfaces

What is happening on the wire?

```
18:39:19.422693 IP 192.51.100.201.646 > 224.0.0.2.646: LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 30
18:39:23.606281 IP 192.51.100.201.646 > 224.0.0.2.646: LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 30
18:39:27.859742 IP 192.51.100.201.646 > 224.0.0.2.646: LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 30
18:39:31.717200 IP 192.51.100.201.646 > 224.0.0.2.646: LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 30
18:39:36.225546 IP 192.51.100.201.646 > 224.0.0.2.646: LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 30
18:39:40.071182 IP 192.51.100.201.646 > 224.0.0.2.646: LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 30

```

Multicast UDP/646

```
f6:10:c7:fb:bb:83 > 01:00:5e:00:00:02, ethertype 802.1Q (0x8100), length 80: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 0, offset 0, flags [none], proto UDP (17), length 62)
    192.51.100.201.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 192.51.100.2:0, pdu-length: 30
	  Hello Message (0x0100), length: 20, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 192.51.100.2
```

Check if CSR2 is ready to receive LDP

```
CSR2#show udp | inc Port|646
Proto        Remote      Port      Local       Port  In Out  Stat TTY OutputIF
 17       --listen--          192.51.100.2      646   0   0 2000001   0 

```


### Now config on CSR1, CSR3 and CSR4

Complete

```
CSR2#
*Aug 22 08:45:13.594: %LDP-5-NBRCHG: LDP Neighbor 192.51.100.1:0 (1) is UP
CSR2#
*Aug 22 08:46:46.393: %LDP-5-NBRCHG: LDP Neighbor 192.51.100.4:0 (2) is UP
```


Still monitoring link I see 2 TCP/646 sessions being created after **Hello Message** UDP/646

```
CSR2#show tcp brief numeric 
TCB       Local Address               Foreign Address             (state)
7F71A7A76F48  192.51.100.2.646           192.51.100.4.52429          ESTAB
7F714EE7B180  192.51.100.2.12159         192.51.100.1.646            ESTAB

```

UDP/646 still listening for Hello/Keepalive

```
CSR2#show udp | inc Port|646    
Proto        Remote      Port      Local       Port  In Out  Stat TTY OutputIF
 17       --listen--          192.51.100.2      646   0   0 2000001   0 
```

Now show **discovery**

```
CSR2#show mpls ldp discovery 
 Local LDP Identifier:
    192.51.100.2:0
    Discovery Sources:
    Interfaces:
	GigabitEthernet2.12 (ldp): xmit/recv
	    LDP Id: 192.51.100.1:0
	GigabitEthernet3.24 (ldp): xmit/recv
	    LDP Id: 192.51.100.4:0
```

**bindings**

```
CSR2#show mpls ldp bindings summary 
Total number of prefixes: 9
Generic label bindings
                      assigned        learned
       prefixes      in labels     out labels
              9              9             17
Total tib route info allocated: 10
Previous tib remote label entries allocated Current/Total: 0/0
Previous tib remote label queues allocated Current/Total: 0/0
```

```
CSR2#show mpls ldp bindings 
  lib entry: 192.51.100.0/24, rev 2
	local binding:  label: imp-null
	remote binding: lsr: 192.51.100.1:0, label: imp-null
  lib entry: 192.51.100.1/32, rev 4
	local binding:  label: 16
	remote binding: lsr: 192.51.100.1:0, label: imp-null
	remote binding: lsr: 192.51.100.4:0, label: 16
  lib entry: 192.51.100.2/32, rev 6
	local binding:  label: imp-null
	remote binding: lsr: 192.51.100.1:0, label: 16
	remote binding: lsr: 192.51.100.4:0, label: 17
  lib entry: 192.51.100.3/32, rev 8
	local binding:  label: 17
	remote binding: lsr: 192.51.100.1:0, label: 17
	remote binding: lsr: 192.51.100.4:0, label: 18
  lib entry: 192.51.100.4/32, rev 10
	local binding:  label: 18
	remote binding: lsr: 192.51.100.1:0, label: 18
	remote binding: lsr: 192.51.100.4:0, label: imp-null
  lib entry: 192.51.100.5/32, rev 19
	no local binding
	remote binding: lsr: 192.51.100.1:0, label: imp-null
  lib entry: 192.51.100.10/31, rev 20
	no local binding
	remote binding: lsr: 192.51.100.1:0, label: imp-null
  lib entry: 192.51.100.200/31, rev 12
	local binding:  label: imp-null
	remote binding: lsr: 192.51.100.1:0, label: imp-null
	remote binding: lsr: 192.51.100.4:0, label: 19
  lib entry: 192.51.100.202/31, rev 14
	local binding:  label: 19
	remote binding: lsr: 192.51.100.1:0, label: imp-null
	remote binding: lsr: 192.51.100.4:0, label: 20
  lib entry: 192.51.100.204/31, rev 16
        local binding:  label: imp-null
	remote binding: lsr: 192.51.100.1:0, label: 19
	remote binding: lsr: 192.51.100.4:0, label: imp-null
  lib entry: 192.51.100.206/31, rev 18
	local binding:  label: 20
	remote binding: lsr: 192.51.100.1:0, label: 20
	remote binding: lsr: 192.51.100.4:0, label: imp-null
```

**lfib**

```
CSR2#show mpls forwarding-table 
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop    
Label      Label      or Tunnel Id     Switched      interface              
16         Pop Label  192.51.100.1/32  0             Gi2.12     192.51.100.200
17         17         192.51.100.3/32  0             Gi2.12     192.51.100.200
           18         192.51.100.3/32  0             Gi3.24     192.51.100.205
18         Pop Label  192.51.100.4/32  0             Gi3.24     192.51.100.205
19         Pop Label  192.51.100.202/31   \
                                       0             Gi2.12     192.51.100.200
20         Pop Label  192.51.100.206/31   \
                                       0             Gi3.24     192.51.100.205

```


