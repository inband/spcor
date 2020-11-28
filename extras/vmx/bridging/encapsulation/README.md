# Encapsulation

Here I'll look at encapsulation a bit closer.

Encapsulation can be specified on IFD (Interface Device) and per-unit IFL (Interface Logical) if ```flexible option``` used.

```
root@VMX2# set interfaces ge-0/0/1 encapsulation ?
Possible completions:
  ethernet-bridge      Ethernet layer-2 bridging
  ethernet-ccc         Ethernet cross-connect
  ethernet-tcc         Ethernet translational cross-connect
  ethernet-vpls        Ethernet virtual private LAN service
  extended-vlan-bridge  VLAN layer-2 bridging
  extended-vlan-ccc    Nonstandard TPID tagging for a cross-connect
  extended-vlan-tcc    802.1q tagging for a translational cross-connect
  extended-vlan-vpls   Extended VLAN virtual private LAN service
  flexible-ethernet-services  Allows per-unit Ethernet encapsulation configuration
  vlan-ccc             802.1q tagging for a cross-connect
  vlan-vci-ccc         CCC for VLAN Q-in-Q and ATM VPI/VCI interworking
  vlan-vpls            VLAN virtual private LAN service

```

In this directory I'll try and give an example of each for the IFD.  




```flexible-ethernet-services``` will be used when drilling 
down IFL's and this may be the best option in SP environment. 

```
root@VMX2# set interfaces ge-0/0/2 unit 1000 encapsulation ?
Possible completions:
  dix                  Ethernet DIXv2 (RFC 894)
  ppp-over-ether       PPPoE encapsulation
  vlan-bridge          VLAN layer-2 bridging
  vlan-ccc             802.1q tagging for a cross-connect
  vlan-tcc             802.1q tagging for a translational cross-connect
  vlan-vpls            VLAN virtual private LAN service
```
