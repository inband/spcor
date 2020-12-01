# routed interface vlan-id 

This is a tagged interface.  

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

```
root@VMX2# run show arp interface ge-0/0/2.222 
MAC Address       Address         Name                      Interface               Flags
da:76:06:9a:e9:17 10.10.222.2     10.10.222.2               ge-0/0/2.222            none
```
