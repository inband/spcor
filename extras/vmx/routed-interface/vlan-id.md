# routed interface vlan-id 

This is a tagged interface.  ```encapsulation``` line is not required for **this** routed interface but interface may provide other services such as bridging.

```vmx2``` - the router

```
root@VMX2# show interfaces ge-0/0/2    
flexible-vlan-tagging;
encapsulation flexible-ethernet-services;
unit 222 {
    vlan-id 222;
    family inet {
        address 10.10.222.1/24;
    }
}

```

```vmx2-host2``` - the host

```
root@vmx2-host2:~# ip link add link eth1 name eth1.222 type vlan id 222 
root@vmx2-host2:~# ip link set dev eth1.222 up
root@vmx2-host2:~# ip addr add 10.10.222.2/24 dev eth1.222
```



```
root@VMX2# run show interfaces ge-0/0/2               
Physical interface: ge-0/0/2, Enabled, Physical link is Up
  Interface index: 150, SNMP ifIndex: 528
  Link-level type: Flexible-Ethernet, MTU: 1522, MRU: 1530, LAN-PHY mode, Speed: 1000mbps, BPDU Error: None,
  Loop Detect PDU Error: None, Ethernet-Switching Error: None, MAC-REWRITE Error: None, Loopback: Disabled,
  Source filtering: Disabled, Flow control: Enabled, Auto-negotiation: Enabled, Remote fault: Online
  Pad to minimum frame size: Disabled
  Device flags   : Present Running
  Interface flags: SNMP-Traps Internal: 0x4000
  CoS queues     : 8 supported, 8 maximum usable queues
  Current address: 02:06:0a:0e:ff:e2, Hardware address: 02:06:0a:0e:ff:e2
  
  
  Logical interface ge-0/0/2.222 (Index 345) (SNMP ifIndex 554)
    Flags: Up SNMP-Traps 0x4000 VLAN-Tag [ 0x8100.222 ]  Encapsulation: ENET2
```

Verify

ip route

```
root@vmx2-host2:~# ip route get 10.10.222.1
10.10.222.1 dev eth1.222 src 10.10.222.2 uid 0 
    cache 
```

ping

```
root@vmx2-host2:~# ping 10.10.222.2
PING 10.10.222.2 (10.10.222.2) 56(84) bytes of data.
64 bytes from 10.10.222.2: icmp_seq=1 ttl=64 time=0.033 ms
64 bytes from 10.10.222.2: icmp_seq=2 ttl=64 time=0.042 ms
64 bytes from 10.10.222.2: icmp_seq=3 ttl=64 time=0.042 ms
```

arp

```
root@vmx2-host2:~# ip neigh 
10.0.0.1 dev eth1.1000 lladdr f2:f0:7b:40:e9:31 REACHABLE
10.10.222.1 dev eth1.222 lladdr 02:06:0a:0e:ff:e2 REACHABLE
```

arp from ```vmx2```

```
root@VMX2# run show arp interface ge-0/0/2.222 
MAC Address       Address         Name                      Interface               Flags
da:76:06:9a:e9:17 10.10.222.2     10.10.222.2               ge-0/0/2.222            none
```

-------------------------------------------------------

To prove ```encapsulation flexible-ethernet-services``` not required.

```
root@VMX2# show interfaces | display set          
set interfaces ge-0/0/0 unit 0 family inet address 192.168.222.82/24
set interfaces ge-0/0/1 encapsulation ethernet-bridge
set interfaces ge-0/0/1 unit 0
set interfaces ge-0/0/2 flexible-vlan-tagging
set interfaces ge-0/0/2 encapsulation flexible-ethernet-services
set interfaces ge-0/0/2 unit 222 vlan-id 222
set interfaces ge-0/0/2 unit 222 family inet address 10.10.222.1/24
set interfaces ge-0/0/2 unit 1000 encapsulation vlan-bridge
set interfaces ge-0/0/2 unit 1000 vlan-id 1000
set interfaces fxp0 unit 0 family inet dhcp vendor-id Juniper-vmx-VM5FC0467850

[edit]
root@VMX2# delete interfaces ge-0/0/2 encapsulation flexible-ethernet-services 

[edit]
root@VMX2# commit 
[edit interfaces ge-0/0/2]
  'unit 1000'
     Link encapsulation type is not valid for device type
error: configuration check-out failed

[edit]
root@VMX2# delete interfaces ge-0/0/2 unit 1000 

[edit]
root@VMX2# commit 
commit complete


```

```
root@VMX2# run ping 10.10.222.2 
PING 10.10.222.2 (10.10.222.2): 56 data bytes
64 bytes from 10.10.222.2: icmp_seq=0 ttl=64 time=13.470 ms
64 bytes from 10.10.222.2: icmp_seq=1 ttl=64 time=4.081 ms
64 bytes from 10.10.222.2: icmp_seq=2 ttl=64 time=594.131 ms
^C64 bytes from 10.10.222.2: icmp_seq=3 ttl=64 time=320.154 ms

--- 10.10.222.2 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max/stddev = 4.081/232.959/594.131/244.238 ms


```

Verification from host

```
root@vmx2-host2:~# tcpdump -nntei eth1.222 icmp
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth1.222, link-type EN10MB (Ethernet), capture size 262144 bytes
02:06:0a:0e:ff:e2 > da:76:06:9a:e9:17, ethertype IPv4 (0x0800), length 98: 10.10.222.1 > 10.10.222.2: ICMP echo request, id 6309, seq 0, length 64
da:76:06:9a:e9:17 > 02:06:0a:0e:ff:e2, ethertype IPv4 (0x0800), length 98: 10.10.222.2 > 10.10.222.1: ICMP echo reply, id 6309, seq 0, length 64
```
