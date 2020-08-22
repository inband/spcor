# verify

MPLS network has been built on top of AS64499 IGP (OSPF) network

AS 664499 has 4 routers participating in MPLS - CSR1, CSR2, CSR3 and CSR4.

I'll check MPLS from CSR1 point of view.

LDP is configured and LFIB has been built.  

CSR1's LFIB

```
CSR1#show mpls forwarding-table 
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop    
Label      Label      or Tunnel Id     Switched      interface              
16         Pop Label  192.51.100.2/32  0             Gi2.12     192.51.100.201
17         Pop Label  192.51.100.3/32  0             Gi3.13     192.51.100.203
18         18         192.51.100.4/32  0             Gi2.12     192.51.100.201
           18         192.51.100.4/32  0             Gi3.13     192.51.100.203
19         Pop Label  192.51.100.204/31   \
                                       0             Gi2.12     192.51.100.201
20         Pop Label  192.51.100.206/31   \
                                       0             Gi3.13     192.51.100.203

```


So from the table CSR1 will **push** label 18 when routing to CSR4 ```192.51.100.4```

CEF shows that there are 2 equal cost routes - both will **push** label 18

```
CSR1#show ip cef 192.51.100.4 detail 
192.51.100.4/32, epoch 2, per-destination sharing
  local label info: global/18
  nexthop 192.51.100.201 GigabitEthernet2.12 label 18
  nexthop 192.51.100.203 GigabitEthernet3.13 label 18
```

Verify reachability with ping

```
CSR1#ping 192.51.100.4 source lo0 
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.51.100.4, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/4 ms

```

Traceroute

```
CSR1#traceroute 192.51.100.4 source Lo0 numeric 
Type escape sequence to abort.
Tracing the route to 192.51.100.4
VRF info: (vrf in name/id, vrf out name/id)
  1 192.51.100.201 [MPLS: Label 18 Exp 0] 3 msec
    192.51.100.203 [MPLS: Label 18 Exp 0] 1 msec
    192.51.100.201 [MPLS: Label 18 Exp 0] 1 msec
  2 192.51.100.207 2 msec
    192.51.100.205 2 msec * 

```

**traceroute mpls**

```
CSR1#traceroute mpls ipv4 192.51.100.4/32 source 192.51.100.1 
more work needed here to demux the tfs subtlv and to display the right output

Codes: '!' - success, 'Q' - request not sent, '.' - timeout,
  'L' - labeled output interface, 'B' - unlabeled output interface, 
  'D' - DS Map mismatch, 'F' - no FEC mapping, 'f' - FEC mismatch,
  'M' - malformed request, 'm' - unsupported tlvs, 'N' - no label entry, 
  'P' - no rx intf label prot, 'p' - premature termination of LSP, 
  'R' - transit router, 'I' - unknown upstream index,
  'l' - Label switched with FEC change, 'd' - see DDMAP for return code,
  'X' - unknown return code, 'x' - return code 0

Type escape sequence to abort.
  0 192.51.100.200 MRU 1500 [Labels: 18 Exp: 0]
L 1 192.51.100.201 MRU 1500 [Labels: implicit-null Exp: 0] 2 ms
! 2 192.51.100.205 3 ms
```



What is happening on the wire?  Traffic TX/RX over link CSR1 to CSR3 (vlan13)



```
CSR1#ping 192.51.100.4 source lo0 repeat 1
Type escape sequence to abort.
Sending 1, 100-byte ICMP Echos to 192.51.100.4, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.1 
!
Success rate is 100 percent (1/1), round-trip min/avg/max = 1/1/1 ms

```



```
root@pve6-lab:~# tcpdump -nntei fwln512i1 'icmp || mpls'
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on fwln512i1, link-type EN10MB (Ethernet), capture size 262144 bytes

aa:00:f9:6f:3e:b0 > 5a:c8:e1:de:a9:3a, ethertype 802.1Q (0x8100), length 122: vlan 13, p 0, ethertype MPLS unicast, MPLS (label 18, exp 0, [S], ttl 255) 192.51.100.1 > 192.51.100.4: ICMP echo request, id 31, seq 0, length 80
5a:c8:e1:de:a9:3a > aa:00:f9:6f:3e:b0, ethertype 802.1Q (0x8100), length 118: vlan 13, p 0, ethertype IPv4, 192.51.100.4 > 192.51.100.1: ICMP echo reply, id 31, seq 0, length 80

```


verbose **-v**

```
aa:00:f9:6f:3e:b0 > 5a:c8:e1:de:a9:3a, ethertype 802.1Q (0x8100), length 122: vlan 13, p 0, ethertype MPLS unicast, MPLS (label 18, exp 0, [S], ttl 255)
	(tos 0x0, ttl 255, id 132, offset 0, flags [none], proto ICMP (1), length 100)
    192.51.100.1 > 192.51.100.4: ICMP echo request, id 32, seq 0, length 80
5a:c8:e1:de:a9:3a > aa:00:f9:6f:3e:b0, ethertype 802.1Q (0x8100), length 118: vlan 13, p 0, ethertype IPv4, (tos 0x0, ttl 254, id 132, offset 0, flags [none], proto ICMP (1), length 100)
    192.51.100.4 > 192.51.100.1: ICMP echo reply, id 32, seq 0, length 80
```


