# BGP

BGP IPv4


This is the BGP session between ```CSR1``` and ```CSR10```

Observations:

* no CoS
* DSCP IP **cs6** tos 0xc0 for both keepalive and ACK


```
root@pve6-lab:~# tcpdump -nnei vmbr0v10 port 179 -v 
tcpdump: listening on vmbr0v10, link-type EN10MB (Ethernet), capture size 262144 bytes
09:20:18.292236 42:c7:46:4c:5e:8d > 0e:c4:7c:99:c3:2a, ethertype 802.1Q (0x8100), length 77: vlan 10, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 6185, offset 0, flags [DF], proto TCP (6), length 59)
    192.51.100.10.29576 > 192.51.100.11.179: Flags [P.], cksum 0x8c80 (correct), seq 2501483817:2501483836, ack 3753644978, win 15548, length 19: BGP
	Keepalive Message (4), length: 19
09:20:18.492316 0e:c4:7c:99:c3:2a > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 58: vlan 10, p 0, ethertype IPv4, (tos 0xc0, ttl 1, id 4356, offset 0, flags [DF], proto TCP (6), length 40)
    192.51.100.11.179 > 192.51.100.10.29576: Flags [.], cksum 0x8ead (correct), ack 19, win 16042, length 0

```

So far all **InterNetwork Control** on Cisco has been DSCP cs6 where IPv4 is used. 
