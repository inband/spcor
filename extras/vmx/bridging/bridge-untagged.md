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
