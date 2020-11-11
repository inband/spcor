# LDP 

```P1```

* add ```family mpls``` to interfaces that need to support mpls
* add interfaces to ```protocol mpls```
* add interfaces to ```protocol ldp```

```
root@VMX1> set cli logical-system P1 
Logical system: P1

root@VMX1:P1> show configuration 
interfaces {
    lt-0/0/0 {
        unit 21 {
            encapsulation vlan;
            vlan-id 12;
            peer-unit 12;
            family inet {
                address 10.0.0.3/31;
            }
            family iso;
            family mpls;
        }
        unit 23 {
            encapsulation vlan;
            vlan-id 23;
            peer-unit 32;
            family inet {
                address 10.0.0.8/31;
            }
            family iso;
            family mpls;
        }
        unit 24 {
            encapsulation vlan;
            vlan-id 24;
            peer-unit 42;
            family inet {
                address 10.0.0.16/31;
            }
            family iso;
            family mpls;
        }
    }
    ge-0/0/6 {
        unit 27 {
            vlan-id 27;
            family inet {
                address 10.0.0.6/31;    
            }
            family iso;
            family mpls;
        }
    }
    ge-0/0/7 {
        unit 227 {
            vlan-id 227;
            family inet {
                address 10.0.0.24/31;
            }
            family iso;
            family mpls;
        }
    }
    ge-0/0/8 {
        unit 25 {
            vlan-id 25;
            family inet {
                address 10.0.0.20/31;
            }
            family iso;
            family mpls;
        }
    }
    
    lo0 {
        unit 2 {
            family inet {
                address 172.16.0.1/32;
            }
            family iso {
                address 49.0000.0000.0002.00;
            }
        }
    }
}
protocols {                             
    mpls {
        interface lt-0/0/0.21;
        interface lt-0/0/0.23;
        interface lt-0/0/0.24;
        interface ge-0/0/6.27;
        interface ge-0/0/7.227;
        interface ge-0/0/8.25;
    }
    isis {
        level 2 wide-metrics-only;
        interface lt-0/0/0.21;
        interface lt-0/0/0.23;
        interface lt-0/0/0.24;
        interface ge-0/0/6.27;
        interface ge-0/0/7.227;
        interface ge-0/0/8.25;
        interface lo0.2;
    }
    ldp {
        track-igp-metric;
        interface lt-0/0/0.21;
        interface lt-0/0/0.23;
        interface lt-0/0/0.24;
        interface ge-0/0/6.27;
        interface ge-0/0/7.227;
        interface ge-0/0/8.25;
    }
}
policy-options {
    policy-statement LB {
        then {
            load-balance per-packet;
        }
    }
}
routing-options {
    forwarding-table {                  
        export LB;
    }
}


```

Verfiy

```
root@VMX1:P1> show ldp neighbor   
Address                             Interface       Label space ID     Hold time
10.0.0.2                            lt-0/0/0.21     172.16.0.11:0        13
10.0.0.9                            lt-0/0/0.23     172.16.0.33:0        13
10.0.0.7                            ge-0/0/6.27     172.16.0.2:0         12
10.0.0.25                           ge-0/0/7.227    172.16.0.2:0         14
10.0.0.21                           ge-0/0/8.25     172.16.0.202:0       12
10.0.0.17                           lt-0/0/0.24     172.16.0.201:0       14
```
---------------------------------------------
