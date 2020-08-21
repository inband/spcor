# message formats

BGP send messages over TCP.

The smallest BGP message is only the BGP header which is 19 Bytes (octets).  Commonly used for keep-alive messages.

The BGP message header is fixed-size, it consists of

* 16 Byte Marker (all 0xFF)
* 2 Byte Length (including hre BGP header (19Bytes to maximum of 4096Bytes)
* 1 Byte Type


There are 4 type codes:

* Open (Type 1)
* Update (2)
* Notification
* Keepalive


All types except the **keepalive** extend the fixed-size header with additional message fields.

### OPEN

```
42:c7:46:4c:5e:8d > 32:14:f3:7d:63:4d, ethertype 802.1Q (0x8100), length 115: vlan 10, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 3924, offset 0, flags [DF], proto TCP (6), length 97)
    192.51.100.10.14055 > 192.51.100.11.179: Flags [P.], cksum 0x2c90 (correct), seq 1:58, ack 1, win 16384, length 57: BGP
	Open Message (1), length: 57
	  Version 4, my AS 64496, Holdtime 180s, ID 192.0.2.10
	  Optional parameters, length: 28
	    Option Capabilities Advertisement (2), length: 6
	      Multiprotocol Extensions (1), length: 4
		AFI IPv4 (1), SAFI Unicast (1)
	    Option Capabilities Advertisement (2), length: 2
	      Route Refresh (Cisco) (128), length: 0
	    Option Capabilities Advertisement (2), length: 2
	      Route Refresh (2), length: 0
	    Option Capabilities Advertisement (2), length: 2
	      Enhanced Route Refresh (70), length: 0
		no decoder for Capability 70
	    Option Capabilities Advertisement (2), length: 6
	      32-Bit AS Number (65), length: 4
		 4 Byte AS 64496

	0x0000:  3214 f37d 634d 42c7 464c 5e8d 8100 000a  2..}cMB.FL^.....
	0x0010:  0800 45c0 0061 0f54 4000 0106 2107 c033  ..E..a.T@...!..3
	0x0020:  640a c033 640b 36e7 00b3 3bb6 ba9f d8ad  d..3d.6...;.....
	0x0030:  09ec 5018 4000 2c90 0000 ffff ffff ffff  ..P.@.,.........
	0x0040:  ffff ffff ffff ffff ffff 0039 0104 fbf0  ...........9....
	0x0050:  00b4 c000 020a 1c02 0601 0400 0100 0102  ................
	0x0060:  0280 0002 0202 0002 0246 0002 0641 0400  .........F...A..
	0x0070:  00fb f0  
```

```
ffff ffff ffff ffff ffff ffff ffff ffff Marker
0039   Length  (57 Bytes)
01     Type (Open Message)
04     Version (4)
fbf0   my AS (64496)
00b4   Hold Time (180s)
c000 020a 1c02 0601   Remote Peer ID (192.0.2.10) 
0400 
0100 0102  
0280 0002 0202 0002 0246 0002 0641 0400 
00fb f0  

```



### UPDATE



### NOTIFICATION



### KEEPALIVE




