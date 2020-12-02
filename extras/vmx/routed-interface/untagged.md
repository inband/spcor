# untagged

So the host has already got an ip address ```10.1.0.2/24``` on interface - from a previous example.

```
3: eth1@if883: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether da:76:06:9a:e9:17 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.2/24 brd 10.1.0.255 scope global eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::d876:6ff:fe9a:e917/64 scope link 
       valid_lft forever preferred_lft forever
```

Now setup the ```vmx2``` with ```10.1.0.10/24```

```
root@VMX2# set interfaces ge-0/0/2 unit 0 family inet address 10.1.0.10/24 

[edit]
root@VMX2# commit 
[edit interfaces ge-0/0/2]
  'unit 0'
     VLAN-ID must be specified on tagged ethernet interfaces
error: configuration check-out failed
```

So flexible-vlan-tagging is configured on IFD - a vlan tag must be associated.

```
root@VMX2# show interfaces ge-0/0/2  
flexible-vlan-tagging;
```

So ``` vlan-id 333``` is used for IFL and at IFD level ```native-vlan-id 333``` is specified.  Other IFL are shown and will also be tested.

```
root@VMX2# show interfaces ge-0/0/2         
flexible-vlan-tagging;
native-vlan-id 333;
unit 0 {
    vlan-id 333;
    family inet {
        address 10.1.0.10/24;
    }
}
unit 222 {
    vlan-id 222;
    family inet {
        address 10.10.222.1/24;
    }
}
unit 223 {
    vlan-tags outer 223 inner 11;
    family inet {
        address 10.10.11.1/24;
    }
}

```

Verify

```
root@VMX2# run show interfaces ge-0/0/2.0       
  Logical interface ge-0/0/2.0 (Index 345) (SNMP ifIndex 551)
    Flags: Up SNMP-Traps 0x4000 VLAN-Tag [ 0x8100.333 ] Native-vlan-id: 333  Encapsulation: ENET2
    Input packets : 0
    Output packets: 1
    Protocol inet, MTU: 1500
    Max nh cache: 75000, New hold nh limit: 75000, Curr nh cnt: 0, Curr new hold cnt: 0, NH drop cnt: 0
      Flags: Sendbcast-pkt-to-re
      Addresses, Flags: Is-Preferred Is-Primary
        Destination: 10.1.0/24, Local: 10.1.0.10, Broadcast: 10.1.0.255
    Protocol multiservice, MTU: Unlimited

```

```
root@VMX2# run ping 10.1.0.2 
PING 10.1.0.2 (10.1.0.2): 56 data bytes
64 bytes from 10.1.0.2: icmp_seq=0 ttl=64 time=774.371 ms
64 bytes from 10.1.0.2: icmp_seq=1 ttl=64 time=488.401 ms
64 bytes from 10.1.0.2: icmp_seq=2 ttl=64 time=85.111 ms
^C
--- 10.1.0.2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max/stddev = 85.111/449.294/774.371/282.745 ms


[edit]
root@VMX2# run show arp                       
MAC Address       Address         Name                      Interface               Flags
da:76:06:9a:e9:17 10.1.0.2        10.1.0.2                  ge-0/0/2.0              none
da:76:06:9a:e9:17 10.10.11.2      10.10.11.2                ge-0/0/2.223            none
da:76:06:9a:e9:17 10.10.222.2     10.10.222.2               ge-0/0/2.222            none
```

Check other IFLs are still working

```
root@VMX2# run ping 10.10.11.2 
PING 10.10.11.2 (10.10.11.2): 56 data bytes
64 bytes from 10.10.11.2: icmp_seq=0 ttl=64 time=761.035 ms
64 bytes from 10.10.11.2: icmp_seq=1 ttl=64 time=673.474 ms
^C
--- 10.10.11.2 ping statistics ---
3 packets transmitted, 2 packets received, 33% packet loss
round-trip min/avg/max/stddev = 673.474/717.255/761.035/43.780 ms

[edit]
root@VMX2# run ping 10.10.222.2   
PING 10.10.222.2 (10.10.222.2): 56 data bytes
64 bytes from 10.10.222.2: icmp_seq=0 ttl=64 time=93.756 ms
64 bytes from 10.10.222.2: icmp_seq=1 ttl=64 time=4.163 ms
^C
--- 10.10.222.2 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max/stddev = 4.163/48.959/93.756/44.796 ms
```

