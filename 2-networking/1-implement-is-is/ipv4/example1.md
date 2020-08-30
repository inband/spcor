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

