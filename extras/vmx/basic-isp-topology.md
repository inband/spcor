# Basic ISP topology


**AS65000**

```PE1```

Platform: Juniper vMX

```
root@VMX1:PE1> show configuration 
interfaces {
    lt-0/0/0 {
        unit 12 {
            encapsulation vlan;
            vlan-id 12;
            peer-unit 21;
            family inet {
                address 10.0.0.2/31;
            }
            family iso;
        }
        unit 112 {
            encapsulation vlan;
            vlan-id 112;
            peer-unit 211;
            family inet {
                address 10.1.0.1/31;
            }
            family iso;
        }
        unit 113 {
            encapsulation vlan;
            vlan-id 113;
            peer-unit 311;
            family inet {
                address 10.1.0.7/31;
            }
            family iso;
        }
    }
    ge-0/0/3 {
        unit 16 {
            vlan-id 16;
            family inet {
                address 10.0.0.0/31;
            }
            family iso;
        }
    }
    lo0 {
        unit 1 {
            family inet {
                address 172.16.0.11/32;
            }
            family iso {
                address 49.0000.0000.0001.00;
            }
        }
    }
}
protocols {
    isis {
        level 2 wide-metrics-only;
        interface lt-0/0/0.12;
        interface ge-0/0/3.16;
        interface lo0.1;
    }

```

Display Set

```
root@VMX1:PE1> show configuration | display set 
set logical-systems PE1 interfaces lt-0/0/0 unit 12 encapsulation vlan
set logical-systems PE1 interfaces lt-0/0/0 unit 12 vlan-id 12
set logical-systems PE1 interfaces lt-0/0/0 unit 12 peer-unit 21
set logical-systems PE1 interfaces lt-0/0/0 unit 12 family inet address 10.0.0.2/31
set logical-systems PE1 interfaces lt-0/0/0 unit 12 family iso
set logical-systems PE1 interfaces lt-0/0/0 unit 112 encapsulation vlan
set logical-systems PE1 interfaces lt-0/0/0 unit 112 vlan-id 112
set logical-systems PE1 interfaces lt-0/0/0 unit 112 peer-unit 211
set logical-systems PE1 interfaces lt-0/0/0 unit 112 family inet address 10.1.0.1/31
set logical-systems PE1 interfaces lt-0/0/0 unit 112 family iso
set logical-systems PE1 interfaces lt-0/0/0 unit 113 encapsulation vlan
set logical-systems PE1 interfaces lt-0/0/0 unit 113 vlan-id 113
set logical-systems PE1 interfaces lt-0/0/0 unit 113 peer-unit 311
set logical-systems PE1 interfaces lt-0/0/0 unit 113 family inet address 10.1.0.7/31
set logical-systems PE1 interfaces lt-0/0/0 unit 113 family iso
set logical-systems PE1 interfaces ge-0/0/3 unit 16 vlan-id 16
set logical-systems PE1 interfaces ge-0/0/3 unit 16 family inet address 10.0.0.0/31
set logical-systems PE1 interfaces ge-0/0/3 unit 16 family iso
set logical-systems PE1 interfaces lo0 unit 1 family inet address 172.16.0.11/32
set logical-systems PE1 interfaces lo0 unit 1 family iso address 49.0000.0000.0001.00
set logical-systems PE1 protocols isis level 2 wide-metrics-only
set logical-systems PE1 protocols isis interface lt-0/0/0.12
set logical-systems PE1 protocols isis interface ge-0/0/3.16
set logical-systems PE1 protocols isis interface lo0.1

```

```
root@VMX1:PE1> show isis adjacency 
Interface             System         L State        Hold (secs) SNPA
ge-0/0/3.16           PE2            2  Up                    8  32:20:a8:23:76:f2

```

---------------------------------------------------

```PE2```

Platform: Cisco XRv

