# ldp

Observations:

* no CoS
* IP DCSP **cs5** tos 0xc0 

```
root@pve6-lab:~# tcpdump -nnei vmbr0v12 port 646 -v
tcpdump: listening on vmbr0v12, link-type EN10MB (Ethernet), capture size 262144 bytes
12:19:42.757334 f6:10:c7:fb:bb:83 > 62:3b:76:19:58:fa, ethertype 802.1Q (0x8100), length 76: vlan 12, p 0, ethertype IPv4, (tos 0xc0, ttl 255, id 33230, offset 0, flags [none], proto TCP (6), length 58)
    192.51.100.2.40480 > 192.51.100.1.646: Flags [.], cksum 0x307e (correct), seq 3015332590:3015332608, ack 4202389139, win 4128, length 18

```
