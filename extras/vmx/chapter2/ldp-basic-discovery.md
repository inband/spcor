# LDP Basic Discovery


LDP Hello message

* UDP/646 to **all-routers** IPv4 multicast ```224.0.0.2``` 
* TTL is 1 - therefore device must be directly connected
* ```Label-Space-ID``` is the Lo0 of originated router as is the ```IPv4 Transport Address```
* Hellos used for discovery so that a TCP/646 session can be established between ```IPv4 Transport Addresses```

```
15:34:45.482290 02:06:0a:0e:ff:f3 > 01:00:5e:00:00:02, ethertype 802.1Q (0x8100), length 88: vlan 16, p 6, ethertype IPv4, (tos 0xc0, ttl 1, id 39621, offset 0, flags [none], proto UDP (17), length 70)
    10.0.0.0.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 172.16.0.11:0, pdu-length: 38
	  Hello Message (0x0100), length: 28, Message ID: 0x00005aa9, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 172.16.0.11
	    Configuration Sequence Number TLV (0x0402), length: 4, Flags: [ignore and don't forward if unknown]
	      Sequence Number: 2
```

```
15:34:47.301726 32:20:a8:23:76:f2 > 01:00:5e:00:00:02, ethertype 802.1Q (0x8100), length 88: vlan 16, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 14749, offset 0, flags [none], proto UDP (17), length 70)
    10.0.0.1.646 > 224.0.0.2.646: 
	LDP, Label-Space-ID: 172.16.0.22:0, pdu-length: 38
	  Hello Message (0x0100), length: 28, Message ID: 0x00000000, Flags: [ignore if unknown]
	    Common Hello Parameters TLV (0x0400), length: 4, Flags: [ignore and don't forward if unknown]
	      Hold Time: 15s, Flags: [Link Hello]
	    IPv4 Transport Address TLV (0x0401), length: 4, Flags: [ignore and don't forward if unknown]
	      IPv4 Transport Address: 172.16.0.22
	    Unknown TLV (0x3ef0), length: 4, Flags: [continue processing and don't forward if unknown]
	      0x0000:  0300 000c
```
