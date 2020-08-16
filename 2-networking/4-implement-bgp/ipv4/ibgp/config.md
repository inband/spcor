# config


CSR1

```
router bgp 64499
 bgp log-neighbor-changes
 neighbor 192.51.100.2 remote-as 64499
 neighbor 192.51.100.2 update-source Loopback0
```

Before issuing update source (note source is ip address 192.51.100.200 associated with egress interface)

```
192.51.100.200.27552 > 192.51.100.2.179: Flags [S], cksum 0x9c57 (correct), seq 3393010700, win 16384, options [mss 1460], length 0
192.51.100.2.179 > 192.51.100.200.28637: Flags [R.], cksum 0xf5f6 (correct), seq 0, ack 1628610309, win 0, length 0    
```

After update source (now Lo0 192.51.100.1)

```
192.51.100.1.18883 > 192.51.100.2.179: Flags [S], cksum 0x0784 (correct), seq 2160082177, win 16384, options [mss 1460], length 0
192.51.100.2.179 > 192.51.100.1.18883: Flags [R.], cksum 0x5f2d (correct), seq 0, ack 2160082178, win 0, length 0
```




CSR2

```
router bgp 64499
 bgp log-neighbor-changes
 neighbor 192.51.100.1 remote-as 64499
 neighbor 192.51.100.1 update-source Loopback0

```


Session established

```
CSR1#show tcp brief numeric | inc state|179
TCB       Local Address               Foreign Address             (state)
7F0E7E795E60  192.51.100.1.179           192.51.100.2.54497          ESTAB
```

#### debug
```
CSR1#
*Aug 16 08:41:54.093: BGP: 192.51.100.2 active went from Idle to Active
*Aug 16 08:41:54.093: BGP: 192.51.100.2 open active, local address 192.51.100.1
*Aug 16 08:41:54.095: BGP: 192.51.100.2 open failed: Connection refused by remote host
*Aug 16 08:41:54.095: BGP: 192.51.100.2 Active open failed - tcb is not available, open active delayed 8192ms (35000ms max, 60% jitter)
*Aug 16 08:41:54.095: BGP: ses global 192.51.100.2 (0x7F0E7E796328:0) act Reset (Active open failed).
*Aug 16 08:41:54.095: BGP: tbl IPv4 Unicast:base Service reset requests
*Aug 16 08:41:54.095: BGP: tbl VPNv4 Unicast:base Service reset requests
*Aug 16 08:41:54.095: BGP: tbl IPv4 Multicast:base Service reset requests
*Aug 16 08:41:54.095: BGP: 192.51.100.2 active went from Active to Idle
*Aug 16 08:41:54.095: BGP: nbr global 192.51.100.2 Active open failed - open timer running
*Aug 16 08:41:54.095: BGP: nbr global 192.51.100.2 Active open failed - open timer running
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive open to 192.51.100.1
*Aug 16 08:41:59.762: BGP: Fetched peer 192.51.100.2 from tcb
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive went from Idle to Connect
*Aug 16 08:41:59.762: BGP: ses global 192.51.100.2 (0x7F0E25BE2828:0) pas Setting open delay timer to 60 seconds.
*Aug 16 08:41:59.762: BGP: ses global 192.51.100.2 (0x7F0E25BE2828:0) pas read request no-op
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive rcv message type 1, length (excl. header) 38
*Aug 16 08:41:59.762: BGP: ses global 192.51.100.2 (0x7F0E25BE2828:0) pas Receive OPEN
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive rcv OPEN, version 4, holdtime 180 seconds
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive rcv OPEN w/ OPTION parameter len: 28
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive rcvd OPEN w/ optional parameter type 2 (Capability) len 6
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive OPEN has CAPABILITY code: 1, length 4
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive OPEN has MP_EXT CAP for afi/safi: 1/1
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive rcvd OPEN w/ optional parameter type 2 (Capability) len 2
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive OPEN has CAPABILITY code: 128, length 0
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive OPEN has ROUTE-REFRESH capability(old) for all address-families
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive rcvd OPEN w/ optional parameter type 2 (Capability) len 2
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive OPEN has CAPABILITY code: 2, length 0
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive OPEN has ROUTE-REFRESH capability(new) for all address-families
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive rcvd OPEN w/ optional parameter type 2 (Capability) len 2
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive OPEN has CAPABILITY code: 70, length 0
*Aug 16 08:41:59.762: BGP: ses global 192.51.100.2 (0x7F0E25BE2828:0) pas Enhanced Refresh cap received in open message
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive rcvd OPEN w/ optional parameter type 2 (Capability) len 6
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive OPEN has CAPABILITY code: 65, length 4
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive OPEN has 4-byte ASN CAP for: 64499
*Aug 16 08:41:59.762: BGP: nbr global 192.51.100.2 neighbor does not have IPv4 MDT topology activated
*Aug 16 08:41:59.762: BGP: 192.51.100.2 passive rcvd OPEN w/ remote AS 64499, 4-byte remote AS 64499
*Aug 16 08:41:59.762: BGP: ses global 192.51.100.2 (0x7F0E25BE2828:0) pas Adding topology IPv4 Unicast:base
*Aug 16 08:41:59.762: BGP: ses global 192.51.100.2 (0x7F0E25BE2828:0) pas Send OPEN
*Aug 16 08:41:59.763: BGP: ses global 192.51.100.2 (0x7F0E25BE2828:0) pas Building Enhanced Refresh capability
*Aug 16 08:41:59.763: BGP: 192.51.100.2 passive went from Connect to OpenSent
*Aug 16 08:41:59.763: BGP: 192.51.100.2 passive sending OPEN, version 4, my as: 64499, holdtime 180 seconds, ID C0336401
*Aug 16 08:41:59.763: BGP: 192.51.100.2 passive went from OpenSent to OpenConfirm
*Aug 16 08:41:59.765: BGP: 192.51.100.2 passive went from OpenConfirm to Established
*Aug 16 08:41:59.765: BGP: ses global 192.51.100.2 (0x7F0E25BE2828:1) pas Assigned ID
*Aug 16 08:41:59.765: BGP: nbr global 192.51.100.2 Stop Active Open timer as all topologies are allocated
*Aug 16 08:41:59.765: BGP: ses global 192.51.100.2 (0x7F0E25BE2828:1) Up
*Aug 16 08:41:59.765: BGP: nopeerup-delay post-boot, set to default, 60s
*Aug 16 08:41:59.765: %BGP-5-ADJCHANGE: neighbor 192.51.100.2 Up 
```


#### tcpdump

```
192.51.100.2.46997 > 192.51.100.1.179: Flags [S], cksum 0x0878 (correct), seq 413939279, win 16384, options [mss 1460], length 0
192.51.100.1.179 > 192.51.100.2.46997: Flags [S.], cksum 0x51f1 (correct), seq 3573014909, ack 413939280, win 16384, options [mss 1460], length 0
192.51.100.2.46997 > 192.51.100.1.179: Flags [.], cksum 0x69ae (correct), seq 1, ack 1, win 16384, length 0
192.51.100.2.46997 > 192.51.100.1.179: Flags [P.], cksum 0x1a42 (correct), seq 1:58, ack 1, win 16384, length 57: BGP
	Open Message (1), length: 57
	  Version 4, my AS 64499, Holdtime 180s, ID 192.51.100.2
	  Optional parameters, length: 28
	    Option Capabilities Advertisement (2), length: 6
	      Multiprotocol Extensions (1), length: 4
		AFI IPv4 (1), SAFI Unicast (1)
		0x0000:  0001 0001
	    Option Capabilities Advertisement (2), length: 2
	      Route Refresh (Cisco) (128), length: 0
	    Option Capabilities Advertisement (2), length: 2
	      Route Refresh (2), length: 0
	    Option Capabilities Advertisement (2), length: 2
	      Enhanced Route Refresh (70), length: 0
		no decoder for Capability 70
	    Option Capabilities Advertisement (2), length: 6
	      32-Bit AS Number (65), length: 4
		 4 Byte AS 64499
		0x0000:  0000 fbf3
192.51.100.1.179 > 192.51.100.2.46997: Flags [.], cksum 0x69ae (correct), seq 1, ack 58, win 16327, length 0
192.51.100.1.179 > 192.51.100.2.46997: Flags [P.], cksum 0x1a43 (correct), seq 1:58, ack 58, win 16327, length 57: BGP
	Open Message (1), length: 57
	  Version 4, my AS 64499, Holdtime 180s, ID 192.51.100.1
	  Optional parameters, length: 28
	    Option Capabilities Advertisement (2), length: 6
	      Multiprotocol Extensions (1), length: 4
		AFI IPv4 (1), SAFI Unicast (1)
		0x0000:  0001 0001
	    Option Capabilities Advertisement (2), length: 2
	      Route Refresh (Cisco) (128), length: 0
	    Option Capabilities Advertisement (2), length: 2
	      Route Refresh (2), length: 0
	    Option Capabilities Advertisement (2), length: 2
	      Enhanced Route Refresh (70), length: 0
		no decoder for Capability 70
	    Option Capabilities Advertisement (2), length: 6
	      32-Bit AS Number (65), length: 4
		 4 Byte AS 64499
		0x0000:  0000 fbf3
192.51.100.1.179 > 192.51.100.2.46997: Flags [P.], cksum 0x6547 (correct), seq 58:77, ack 58, win 16327, length 19: BGP
	Keepalive Message (4), length: 19
192.51.100.2.46997 > 192.51.100.1.179: Flags [.], cksum 0x6975 (correct), seq 58, ack 77, win 16308, length 0
192.51.100.2.46997 > 192.51.100.1.179: Flags [P.], cksum 0x6547 (correct), seq 58:77, ack 77, win 16308, length 19: BGP
	Keepalive Message (4), length: 19
192.51.100.1.179 > 192.51.100.2.46997: Flags [P.], cksum 0x6534 (correct), seq 77:96, ack 77, win 16308, length 19: BGP
	Keepalive Message (4), length: 19
192.51.100.1.179 > 192.51.100.2.46997: Flags [P.], cksum 0x6719 (correct), seq 96:119, ack 77, win 16308, length 23: BGP
	Update Message (2), length: 23
	  End-of-Rib Marker (empty NLRI)
192.51.100.2.46997 > 192.51.100.1.179: Flags [.], cksum 0x6962 (correct), seq 77, ack 119, win 16266, length 0
192.51.100.2.46997 > 192.51.100.1.179: Flags [P.], cksum 0x6534 (correct), seq 77:96, ack 119, win 16266, length 19: BGP
	Keepalive Message (4), length: 19
192.51.100.2.46997 > 192.51.100.1.179: Flags [P.], cksum 0x6719 (correct), seq 96:119, ack 119, win 16266, length 23: BGP
	Update Message (2), length: 23
	  End-of-Rib Marker (empty NLRI)
192.51.100.1.179 > 192.51.100.2.46997: Flags [.], cksum 0x6938 (correct), seq 119, ack 119, win 16266, length 0



    192.51.100.1.179 > 192.51.100.2.46997: Flags [P.], cksum 0x650a (correct), seq 119:138, ack 119, win 16266, length 19: BGP
	Keepalive Message (4), length: 19
    192.51.100.2.46997 > 192.51.100.1.179: Flags [.], cksum 0x6938 (correct), seq 119, ack 138, win 16247, length 0
    192.51.100.2.46997 > 192.51.100.1.179: Flags [P.], cksum 0x650a (correct), seq 119:138, ack 138, win 16247, length 19: BGP
	Keepalive Message (4), length: 19
```




