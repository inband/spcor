# example 3

This example will check the wire.  VC is UP.


CSR1 (PE)

```
CSR1#show mpls l2transport vc 1001        

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5.100        Eth VLAN 100               192.51.100.4    1001       UP        

```

VC 1001 labels

```
CSR1#show mpls l2transport binding 1001
  Destination Address: 192.51.100.4,VC ID: 1001
    Local Label:  25
        Cbit: 1,    VC Type: Ethernet,    GroupID: 0
        MTU: 1500,   Interface Desc: n/a
        VCCV: CC Type: CW [1], RA [2], TTL [3]
              CV Type: LSPV [2]
    Remote Label: 23
        Cbit: 1,    VC Type: Ethernet,    GroupID: 0
        MTU: 1500,   Interface Desc: n/a
        VCCV: CC Type: CW [1], RA [2], TTL [3]
              CV Type: LSPV [2]

```



CSR2 (PE)

```
CSR4#show mpls l2transport vc 1001 

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5.100        Eth VLAN 100               192.51.100.1    1001       UP        

```

Check reachability

```
CUSTX-CPE1#ping vrf CUST1 10.1.1.11
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.11, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/3 ms
```


Now what is happening on wire for **ping**?

CSR1

Outgoing via Gi2.12 ```d6:0e:10:83:ae:ff > f6:10:c7:fb:bb:83```
* vlan 12
* MPLS label 16 (top)
* MPLS (VC) label 23 (bottom of stack)
* ETHERTYPE/IP/ICMP starts ```0x0010:  0800 4```




```
root@pve6-lab:~# tcpdump -nntei vmbr0v12 mpls
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on vmbr0v12, link-type EN10MB (Ethernet), capture size 262144 bytes
d6:0e:10:83:ae:ff > f6:10:c7:fb:bb:83, ethertype 802.1Q (0x8100), length 144: vlan 12, p 0, ethertype MPLS unicast, MPLS (label 16, exp 0, ttl 255) (label 23, exp 0, [S], ttl 255)
	0x0000:  0000 0000 6ec2 4776 d31c a2df d1ed e9d5  ....n.Gv........
	0x0010:  0800 4500 0064 0028 0000 ff01 a55a 0a01  ..E..d.(.....Z..
	0x0020:  010a 0a01 010b 0800 88d2 0008 0000 0000  ................
	0x0030:  0000 00d8 f497 abcd abcd abcd abcd abcd  ................
	0x0040:  abcd abcd abcd abcd abcd abcd abcd abcd  ................
	0x0050:  abcd abcd abcd abcd abcd abcd abcd abcd  ................
	0x0060:  abcd abcd abcd abcd abcd abcd abcd abcd  ................
	0x0070:  abcd abcd abcd                           ......
```

Incoming also via Gi2.12 ```f6:10:c7:fb:bb:83 > d6:0e:10:83:ae:ff```
* vlan 12
* MPLS label 25 (bottom of the stack)
* ETHERTYPE/IP/ICMP starts ```0x0010:  0800 4```

```
f6:10:c7:fb:bb:83 > d6:0e:10:83:ae:ff, ethertype 802.1Q (0x8100), length 140: vlan 12, p 0, ethertype MPLS unicast, MPLS (label 25, exp 0, [S], ttl 254)
	0x0000:  0000 0000 a2df d1ed e9d5 6ec2 4776 d31c  ..........n.Gv..
	0x0010:  0800 4500 0064 0028 0000 ff01 a55a 0a01  ..E..d.(.....Z..
	0x0020:  010b 0a01 010a 0000 90d2 0008 0000 0000  ................
	0x0030:  0000 00d8 f497 abcd abcd abcd abcd abcd  ................
	0x0040:  abcd abcd abcd abcd abcd abcd abcd abcd  ................
	0x0050:  abcd abcd abcd abcd abcd abcd abcd abcd  ................
	0x0060:  abcd abcd abcd abcd abcd abcd abcd abcd  ................
	0x0070:  abcd abcd abcd

```

```
CSR1#show mpls forwarding-table labels 25
Local      Outgoing   Prefix           Bytes Label   Outgoing   Next Hop    
Label      Label      or Tunnel Id     Switched      interface              
25         No Label   l2ckt(2)         4076          Gi5.100    point2point 
```

