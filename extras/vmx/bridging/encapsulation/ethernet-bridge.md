# ethernet bridge

Ethernet Bridge will place IFD into bridge.  This is known as an access port or untagged.

```ge-0/0/1``` is configured as ```ethernet-bridge```

```
ge-0/0/1 {
    encapsulation ethernet-bridge;
    unit 0;
}
ge-0/0/2 {
    flexible-vlan-tagging;
    encapsulation flexible-ethernet-services;
    unit 1000 {
        encapsulation vlan-bridge;
        vlan-id 1000;
    }
}

bridge-domains {
    VLAN-1000 {
        vlan-id 1000;
        interface ge-0/0/1.0;
        interface ge-0/0/2.1000;
    }
}

```

Note - a physical interface (IFD) cannot be added to bridge-domain, if you add ```interface ge-0/0/1``` to a bridge domain Junos will automatically change
to interface ```ge-0/0/1.0```

Therefore IFL ```unit 0 ``` is required.  Other IFL numbers are invalid in this configuration.

```
root@VMX2# run show interfaces ge-0/0/1.0 
  Logical interface ge-0/0/1.0 (Index 343) (SNMP ifIndex 552)
    Flags: Up SNMP-Traps 0x24004000 VLAN-Tag [  ] In(push 0x8100.1000) Out(pop)  Encapsulation: Ethernet-Bridge
    Input packets : 486
    Output packets: 486
    Protocol bridge, MTU: 1514
      Flags: Is-Primary
```

Given the ```bridge-domains ``` vlan 1000 is pushed on ingress, and vlan 1000 is popped on egress.
