# exmaple 1

Example1: Basic IPv4 Config (Jeff Doyle's TCP/IP Vol I)


Setup IS-IS CSR11 to CSR13 - dynamically route Lo0's 

* configure IS-IS routing process
* configure NET (using Gi1 MAC)
* add to interface


First CSR13

```
hostname CSR13
!
interface Loopback0
 ip address 172.30.0.3 255.255.255.255
!
interface GigabitEthernet2.1113
 encapsulation dot1Q 1113
 ip address 172.30.0.203 255.255.255.254
 ip router isis 
!
router isis
 net 00.0001.de98.a23c.738c.00
 metric-style narrow
!

```

What is happening on wire?

```
root@pve6-lab:~# tcpdump -nntei vmbr0v1113
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on vmbr0v1113, link-type EN10MB (Ethernet), capture size 262144 bytes
0a:77:2a:f4:04:77 > 01:80:c2:00:00:14, ethertype 802.1Q (0x8100), length 1518: vlan 1113, p 0, LLC, dsap OSI (0xfe) Individual, ssap OSI (0xfe) Command, ctrl 0x03: OSI NLPID IS-IS (0x83): L1 Lan IIH, src-id de98.a23c.738c, lan-id de98.a23c.738c.01, prio 64, length 1497
0a:77:2a:f4:04:77 > 01:80:c2:00:00:15, ethertype 802.1Q (0x8100), length 1518: vlan 1113, p 0, LLC, dsap OSI (0xfe) Individual, ssap OSI (0xfe) Command, ctrl 0x03: OSI NLPID IS-IS (0x83): L2 Lan IIH, src-id de98.a23c.738c, lan-id de98.a23c.738c.01, prio 64, length 1497
0a:77:2a:f4:04:77 > 01:80:c2:00:00:14, ethertype 802.1Q (0x8100), length 1518: vlan 1113, p 0, LLC, dsap OSI (0xfe) Individual, ssap OSI (0xfe) Command, ctrl 0x03: OSI NLPID IS-IS (0x83): L1 Lan IIH, src-id de98.a23c.738c, lan-id de98.a23c.738c.01, prio 64, length 1497
0a:77:2a:f4:04:77 > 01:80:c2:00:00:15, ethertype 802.1Q (0x8100), length 1518: vlan 1113, p 0, LLC, dsap OSI (0xfe) Individual, ssap OSI (0xfe) Command, ctrl 0x03: OSI NLPID IS-IS (0x83): L2 Lan IIH, src-id de98.a23c.738c, lan-id de98.a23c.738c.01, prio 64, length 1497
```

Verbose

```
root@pve6-lab:~# tcpdump -nntei vmbr0v1113 -v
tcpdump: listening on vmbr0v1113, link-type EN10MB (Ethernet), capture size 262144 bytes
0a:77:2a:f4:04:77 > 01:80:c2:00:00:14, ethertype 802.1Q (0x8100), length 1518: vlan 1113, p 0, LLC, dsap OSI (0xfe) Individual, ssap OSI (0xfe) Command, ctrl 0x03: OSI NLPID IS-IS (0x83): length 1497
	L1 Lan IIH, hlen: 27, v: 1, pdu-v: 1, sys-id-len: 6 (0), max-area: 3 (0)
	  source-id: de98.a23c.738c,  holding time: 30s, Flags: [Level 1, Level 2]
	  lan-id:    de98.a23c.738c.01, Priority: 64, PDU length: 1497
	    Protocols supported TLV #129, length: 1
	      NLPID(s): IPv4 (0xcc)
	    Area address(es) TLV #1, length: 4
	      Area address (length: 3): 00.0001
	    IPv4 Interface address(es) TLV #132, length: 4
	      IPv4 interface address: 172.30.0.203
	    Restart Signaling TLV #211, length: 3
	      Flags [none], Remaining holding time 0s
	    Padding TLV #8, length: 255
	    Padding TLV #8, length: 255
	    Padding TLV #8, length: 255
	    Padding TLV #8, length: 255
	    Padding TLV #8, length: 255
	    Padding TLV #8, length: 163
0a:77:2a:f4:04:77 > 01:80:c2:00:00:15, ethertype 802.1Q (0x8100), length 1518: vlan 1113, p 0, LLC, dsap OSI (0xfe) Individual, ssap OSI (0xfe) Command, ctrl 0x03: OSI NLPID IS-IS (0x83): length 1497
	L2 Lan IIH, hlen: 27, v: 1, pdu-v: 1, sys-id-len: 6 (0), max-area: 3 (0)
	  source-id: de98.a23c.738c,  holding time: 30s, Flags: [Level 1, Level 2]
	  lan-id:    de98.a23c.738c.01, Priority: 64, PDU length: 1497
	    Protocols supported TLV #129, length: 1
	      NLPID(s): IPv4 (0xcc)
	    Area address(es) TLV #1, length: 4
	      Area address (length: 3): 00.0001
	    IPv4 Interface address(es) TLV #132, length: 4
	      IPv4 interface address: 172.30.0.203
	    Restart Signaling TLV #211, length: 3
	      Flags [none], Remaining holding time 0s
	    Padding TLV #8, length: 255
	    Padding TLV #8, length: 255
	    Padding TLV #8, length: 255
	    Padding TLV #8, length: 255
	    Padding TLV #8, length: 255
	    Padding TLV #8, length: 163

```


CSR11

```
hostname csr11
!
interface Loopback0
 ip address 172.30.0.1 255.255.255.255
!
interface GigabitEthernet3.1113
 encapsulation dot1Q 1113
 ip address 172.30.0.202 255.255.255.254
 ip router isis 
!
```

No dynamic routing - yet.  Add lo0

```
csr11#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      172.30.0.0/16 is variably subnetted, 5 subnets, 2 masks
C        172.30.0.1/32 is directly connected, Loopback0
C        172.30.0.200/31 is directly connected, GigabitEthernet2.1112
L        172.30.0.200/32 is directly connected, GigabitEthernet2.1112
C        172.30.0.202/31 is directly connected, GigabitEthernet3.1113
L        172.30.0.202/32 is directly connected, GigabitEthernet3.1113

csr11#conf t
Enter configuration commands, one per line.  End with CNTL/Z.

csr11(config)#int lo0
csr11(config-if)#ip router isis 
```


debug

```
CSR13#debug isis rib local 
IS-IS local RIB debugging is on for IPv4 unicast topology base
CSR13#term
CSR13#terminal mo
CSR13#terminal monitor 
CSR13#
*Aug 30 07:13:55.428: ISIS-LR: 172.30.0.202/31: Looking for RT - 
*Aug 30 07:13:55.428: ISIS-LR:   RT exists
*Aug 30 07:13:55.428: ISIS-LR:   path exists: [115/40/20] via 172.30.0.202(Gi2.1113) from 172.30.0.202 tg 0 sid 4294967295 strict-sid 4294967295 attr 0x0 LSP[4/(1->2)] pfx_attr(R:0 X:0 N:0) pfx_attr_present:0 
*Aug 30 07:13:55.428: ISIS-LR: 172.30.0.202/31: path change on LSP[4/2] [(115->115)/(40->40)/(20->20)] via (172.30.0.202->172.30.0.202)((Gi2.1113)->(Gi2.1113)) from(172.30.0.202->172.30.0.1) tg(0->0) attr(0x0->0x0)SID(4294967295->4294967295) pfx_attr((R:0 X:0 N:0)->(R:0 X:0 N:0)),
*Aug 30 07:13:55.428: ISIS-LR: Update RT prefix attr (R:0X:0N:0)->(R:0X:0N:0)
*Aug 30 07:13:55.428: ISIS-LR:   Enqueued to updateQ[2] for 172.30.0.202/31
*Aug 30 07:13:55.428: ISIS-LR: 172.30.0.1/32: Looking for RT - 
*Aug 30 07:13:55.429: ISIS-LR: 172.30.0.1/32: Create new RT
*Aug 30 07:13:55.429: ISIS-LR: 172.30.0.1/32: create new path: [115/40/20] via 172.30.0.202(Gi2.1113) from 172.30.0.202 tag 0 sid 4294967295 strict-sid 4294967295 LSP[4/2]  attr:0x0
*Aug 30 07:13:55.429: ISIS-LR: Update RT prefix attr (R:0X:0N:0)->(R:0X:0N:0)
*Aug 30 07:13:55.429: ISIS-LR:   Enqueued to updateQ[1] for 172.30.0.1/32
*Aug 30 07:13:55.429: ISIS-LR: 172.30.0.1/32: not aged out in LSP ix 4 same ver(2)
*Aug 30 07:13:55.429: ISIS-LR: 172.30.0.202/31: not aged out in LSP ix 4 same ver(2)
*Aug 30 07:13:55.429: ISIS-LR: 172.30.0.202/31: Looking for RT - 
*Aug 30 07:13:55.429: ISIS-LR:   RT exists
*Aug 30 07:13:55.429: ISIS-LR:   path exists: [115/80/20] via 172.30.0.202(Gi2.1113) from 172.30.0.202 tg 0 sid 4294967295 strict-sid 4294967295 attr 0x0 LSP[5/(2->3)] pfx_attr(R:0 X:0 N:0) pfx_attr_present:0 
*Aug 30 07:13:55.429: ISIS-LR: 172.30.0.202/31: path change on LSP[5/3] [(115->115)/(80->80)/(20->20)] via (172.30.0.202->172.30.0.202)((Gi2.1113)->(Gi2.1113)) from(172.30.0.202->172.30.0.1) tg(0->0) attr(0x0->0x0)SID(4294967295->4294967295) pfx_attr((R:0 X:0 N:0)->(R:0 X:0 N:0)),
*Aug 30 07:13:55.430: ISIS-LR: 172.30.0.1/32: Looking for RT - 
*Aug 30 07:13:55.430: ISIS-LR:   RT exists
*Aug 30 07:13:55.430: ISIS-LR: 172.30.0.1/32: create new path: [115/80/20] via 172.30.0.202(Gi2.1113) from 172.30.0.202 tag 0 sid 4294967295 strict-sid 4294967295 LSP[5/3]  attr:0x0
*Aug 30 07:13:55.430: ISIS-LR: 172.30.0.1/32: not aged out in LSP ix 5 same ver(3)
*Aug 30 07:13:55.430: ISIS-LR: 172.30.0.202/31: not aged out in LSP ix 5 same ver(3)

```

Check on CSR13

```
CSR13#show ip route isis 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      172.30.0.0/16 is variably subnetted, 8 subnets, 2 masks
i L1     172.30.0.1/32 
           [115/20] via 172.30.0.202, 00:02:10, GigabitEthernet2.1113

```

Add Lo0 to ISIS on CSR13


```
CSR13(config)#int Lo0
CSR13(config-if)#ip router isis 
CSR13(config-if)#
*Aug 30 07:16:58.190: ISIS-LR: 172.30.0.202/31: Looking for RT - 
*Aug 30 07:16:58.190: ISIS-LR:   RT exists
*Aug 30 07:16:58.190: ISIS-LR:   path exists: [115/80/20] via 172.30.0.202(Gi2.1113) from 172.30.0.1 tg 0 sid 4294967295 strict-sid 4294967295 attr 0x0 LSP[5/(3->4)] pfx_attr(R:0 X:0 N:0) pfx_attr_present:1 
*Aug 30 07:16:58.190: ISIS-LR: 172.30.0.202/31: path change on LSP[5/4] [(115->115)/(80->80)/(20->20)] via (172.30.0.202->172.30.0.202)((Gi2.1113)->(Gi2.1113)) from(172.30.0.1->172.30.0.1) tg(0->0) attr(0x0->0x0)SID(4294967295->4294967295) pfx_attr((R:0 X:0 N:0)->(R:0 X:0 N:0)),
*Aug 30 07:16:58.190: ISIS-LR: 172.30.0.1/32: Looking for RT - 
*Aug 30 07:16:58.190: ISIS-LR:   RT exists
*Aug 30 07:16:58.190: ISIS-LR:   path exists: [115/80/20] via 172.30.0.202(Gi2.1113) from 172.30.0.1 tg 0 sid 4294967295 strict-sid 4294967295 attr 0x0 LSP[5/(3->4)] pfx_attr(R:0 X:0 N:0) pfx_attr_present:1 
*Aug 30 07:16:58.191: ISIS-LR: 172.30.0.1/32: path change on LSP[5/4] [(115->115)/(80->80)/(20->20)] via (172.30.0.202->172.30.0.202)((Gi2.1113)->(Gi2.1113)) from(172.30.0.1->172.30.0.1) tg(0->0) attr(0x0->0x0)SID(4294967295->4294967295) pfx_attr((R:0 X:0 N:0)->(R:0 X:0 N:0)),
*Aug 30 07:16:58.191: ISIS-LR: 172.30.0.3/32: Looking for RT - 
*Aug 30 07:16:58.191: ISIS-LR: 172.30.0.3/32: Create new RT
*Aug 30 07:16:58.191: ISIS-LR: 172.30.0.3/32: create new path: [115/80/30] via 172.30.0.202(Gi2.1113) from 172.30.0.202 tag 0 sid 4294967295 strict-sid 4294967295 LSP[5/4]  attr:0x0
*Aug 30 07:16:58.191: ISIS-LR: Update RT prefix attr (R:0X:0N:0)->(R:0X:0N:0)
*Aug 30 07:16:58.191: ISIS-LR:   Enqueued to updateQ[1] for 172.30.0.3/32
*Aug 30 07:16:58.191: ISIS-LR: 172.30.0.3/32: not aged out in LSP ix 5 same ver(4)
*Aug 30 07:16:58.191: ISIS-LR: 172.30.0.1/32: not aged out in LSP ix 5 same ver(4)
*Aug 30 07:16:58.191: ISIS-LR: 172.30.0.202/31: not aged out in LSP ix 5 same ver(4)

```


Back on CSR11.  Show route test reachibility.

```
csr11#show ip route isis 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      172.30.0.0/16 is variably subnetted, 6 subnets, 2 masks
i L1     172.30.0.3/32 
           [115/20] via 172.30.0.203, 00:01:07, GigabitEthernet3.1113

```

Ping CSR13 ```172.30.0.3``` from CSR11 ```Lo0```

```
csr11#ping 172.30.0.3 source Lo0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.30.0.3, timeout is 2 seconds:
Packet sent with a source address of 172.30.0.1 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
```

