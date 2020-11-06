# Logical Systems

**Note** Basic ISP Topology from MPLS in the SDN Era

My goal is to build the entire topology.  The Junos routers that interconnect to other Junos will use ```logical tunnels``` interfaces ```lt-0/0/0```.
Physical interfaces ```ge-0/0/-*``` will be used to connect to Cisco XRs.


To enable ```logical tunnels```

```
root@VMX1> show configuration chassis                    
fpc 0 {
    pic 0 {
        tunnel-services;
    }
    lite-mode;
}
```
display set

```
set chassis fpc 0 pic 0 tunnel-services
set chassis fpc 0 lite-mode
```

### Logical systems

```
root@VMX1> show configuration logical-systems 
CE1;
CE2;
P1 {
    interfaces {
        lt-0/0/0 {
            unit 32 {
                encapsulation vlan;
                vlan-id 23;
                peer-unit 23;
                family inet {
                    address 10.0.0.3/31;
                }
            }
        }
    }
}
PE1 {
    interfaces {
        lt-0/0/0 {
            unit 23 {
                encapsulation vlan;
                vlan-id 23;
                peer-unit 32;
                family inet {
                    address 10.0.0.2/31;
                }
            }
        }
    }
}
RR1;

```

display set

```
root@VMX1> show configuration logical-systems | display set 
set logical-systems CE1
set logical-systems CE2
set logical-systems P1 interfaces lt-0/0/0 unit 32 encapsulation vlan
set logical-systems P1 interfaces lt-0/0/0 unit 32 vlan-id 23
set logical-systems P1 interfaces lt-0/0/0 unit 32 peer-unit 23
set logical-systems P1 interfaces lt-0/0/0 unit 32 family inet address 10.0.0.3/31
set logical-systems PE1 interfaces lt-0/0/0 unit 23 encapsulation vlan
set logical-systems PE1 interfaces lt-0/0/0 unit 23 vlan-id 23
set logical-systems PE1 interfaces lt-0/0/0 unit 23 peer-unit 32
set logical-systems PE1 interfaces lt-0/0/0 unit 23 family inet address 10.0.0.2/31
set logical-systems RR1

```
