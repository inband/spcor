# bridge untagged

```
set interfaces ge-0/0/1 flexible-vlan-tagging
set interfaces ge-0/0/1 native-vlan-id 1000
set interfaces ge-0/0/1 encapsulation flexible-ethernet-services
set interfaces ge-0/0/1 unit 0 encapsulation vlan-bridge
set interfaces ge-0/0/1 unit 0 vlan-id 1000

set interfaces ge-0/0/2 flexible-vlan-tagging
set interfaces ge-0/0/2 native-vlan-id 1000
set interfaces ge-0/0/2 encapsulation flexible-ethernet-services
set interfaces ge-0/0/2 unit 0 encapsulation vlan-bridge
set interfaces ge-0/0/2 unit 0 vlan-id 1000

set bridge-domains VLAN-1000 vlan-id 1000
set bridge-domains VLAN-1000 interface ge-0/0/1.0
set bridge-domains VLAN-1000 interface ge-0/0/2.0
```


```
root@VMX2> show interfaces ge-0/0/1.0  
  Logical interface ge-0/0/1.0 (Index 343) (SNMP ifIndex 552)
    Flags: Up SNMP-Traps 0x20004000
    VLAN-Tag [ 0x8100.1000 ] Native-vlan-id: 1000  Encapsulation: VLAN-Bridge
    Input packets : 2727
    Output packets: 2673
    Protocol bridge, MTU: 1522
      Flags: Is-Primary
```




Untagged bridge domain (alternative) see ```ge-0/0/2```

```
set interfaces ge-0/0/1 flexible-vlan-tagging
set interfaces ge-0/0/1 native-vlan-id 1000
set interfaces ge-0/0/1 encapsulation flexible-ethernet-services
set interfaces ge-0/0/1 unit 0 encapsulation vlan-bridge
set interfaces ge-0/0/1 unit 0 vlan-id 1000

set interfaces ge-0/0/2 encapsulation ethernet-bridge
set interfaces ge-0/0/2 unit 0

set bridge-domains VLAN-1000 vlan-id 1000
set bridge-domains VLAN-1000 interface ge-0/0/1.0
set bridge-domains VLAN-1000 interface ge-0/0/2.0

```


```
root@VMX2> show interfaces ge-0/0/2.0    
  Logical interface ge-0/0/2.0 (Index 342) (SNMP ifIndex 551)
    Flags: Up SNMP-Traps 0x24004000
    VLAN-Tag [  ] In(push 0x8100.1000) Out(pop)  Encapsulation: Ethernet-Bridge
    Input packets : 358
    Output packets: 359
    Protocol bridge, MTU: 1514
```
