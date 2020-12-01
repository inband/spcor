# routed interface vlan-id 

This is a tagged interface.  

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




