# vlan-tags

This option is for qinq - stacking vlans.

Configure router - ```vmx2```

```
root@VMX2# show interfaces ge-0/0/2        
flexible-vlan-tagging;
unit 223 {
    vlan-tags outer 223 inner 11;
    family inet {
        address 10.10.11.1/24;
    }
}
```

Without configuring host start pinging.

```
root@VMX2# run ping 10.10.11.2 
PING 10.10.11.2 (10.10.11.2): 56 data bytes

```

Should see broadcast arp on host - ```vmx2-host2```

```
root@vmx2-host2:~# tcpdump -nntei eth1 broadcast
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
02:06:0a:0e:ff:e2 > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 50: vlan 223, p 0, ethertype 802.1Q, vlan 11, p 0, ethertype ARP, Request who-has 10.10.11.2 tell 10.10.11.1, length 28
02:06:0a:0e:ff:e2 > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 50: vlan 223, p 0, ethertype 802.1Q, vlan 11, p 0, ethertype ARP, Request who-has 10.10.11.2 tell 10.10.11.1, length 28
```

Verbose

```
root@vmx2-host2:~# tcpdump -nnXXtei eth1 broadcast -vv
tcpdump: listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
02:06:0a:0e:ff:e2 > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 50: vlan 223, p 0, ethertype 802.1Q, vlan 11, p 0, ethertype ARP, Ethernet (len 6), IPv4 (len 4), Request who-has 10.10.11.2 tell 10.10.11.1, length 28
        0x0000:  ffff ffff ffff 0206 0a0e ffe2 8100 00df  ................
        0x0010:  8100 000b 0806 0001 0800 0604 0001 0206  ................
        0x0020:  0a0e ffe2 0a0a 0b01 0000 0000 0000 0a0a  ................
        0x0030:  0b02       

```

Interesting enough - Juniper is using 0x8100 in 0x8100.  I though Juniper used 0x88A8 by default.  THis can also be seen below on the ```vmx2```

```
root@VMX2# run show interfaces ge-0/0/2.223
  Logical interface ge-0/0/2.223 (Index 342) (SNMP ifIndex 555)
    Flags: Up SNMP-Traps 0x4000 VLAN-Tag [ 0x8100.223 0x8100.11 ]  Encapsulation: ENET2
    Input packets : 132
    Output packets: 782
    Protocol inet, MTU: 1500
    Max nh cache: 75000, New hold nh limit: 75000, Curr nh cnt: 1, Curr new hold cnt: 0, NH drop cnt: 0
      Flags: Sendbcast-pkt-to-re
      Addresses, Flags: Is-Preferred Is-Primary
        Destination: 10.10.11/24, Local: 10.10.11.1, Broadcast: 10.10.11.255
    Protocol multiservice, MTU: Unlimited
```



Ok - define ```eth1.223.11``

```
root@vmx2-host2:~# ip link add link eth1 name eth1.223 type vlan id 223
root@vmx2-host2:~# ip link add link eth1.223 name eth1.223.11 type vlan id 11 
root@vmx2-host2:~# ip addr add 10.10.11.2/24 dev eth1.223.11

```

Bring interface up

```
root@vmx2-host2:~# ip link set dev eth1.223.11 up
RTNETLINK answers: Network is down
root@vmx2-host2:~# ip link set dev eth1.223 up
root@vmx2-host2:~# ip link set dev eth1.223.11 up

```

Verify reachability

```
root@VMX2# run ping 10.10.11.2 
PING 10.10.11.2 (10.10.11.2): 56 data bytes
64 bytes from 10.10.11.2: icmp_seq=547 ttl=64 time=4369.967 ms
64 bytes from 10.10.11.2: icmp_seq=548 ttl=64 time=3368.527 ms
64 bytes from 10.10.11.2: icmp_seq=549 ttl=64 time=2365.833 ms
64 bytes from 10.10.11.2: icmp_seq=550 ttl=64 time=1356.941 ms
64 bytes from 10.10.11.2: icmp_seq=551 ttl=64 time=359.825 ms
```

arp

```
root@VMX2# run show arp   
MAC Address       Address         Name                      Interface               Flags
da:76:06:9a:e9:17 10.10.11.2      10.10.11.2                ge-0/0/2.223            none
da:76:06:9a:e9:17 10.10.222.2     10.10.222.2               ge-0/0/2.222            none
```

Above shows same learned(dst) MAC from qinq interface as well as the single dot1q routed interface.



