# example 4

Example4: cust different vlans at each PE (vlan rewrite)

* CUST1-CP1 will be ```vlan 100```

* CUST1-CP2 will be ```vlan 101```


CUST1-CPE1

```
hostname CUSTX-CPE1
!         
interface GigabitEthernet2.100
 encapsulation dot1Q 100
 vrf forwarding CUST1
 ip address 10.1.1.10 255.255.255.254
!

```

CUST1-CPE2

```
hostname CUSTX-CPE2
!
interface GigabitEthernet2.101
 encapsulation dot1Q 101
 vrf forwarding CUST1
 ip address 10.1.1.11 255.255.255.254
!

```


CSR1
```
hostname CSR1

!
interface GigabitEthernet5.100
 encapsulation dot1Q 100
 xconnect 192.51.100.4 1001 encapsulation mpls pw-class CUST1
```

CSR4
```
hostname CSR4
!
interface GigabitEthernet5.101
 encapsulation dot1Q 101
 xconnect 192.51.100.1 1001 encapsulation mpls pw-class CUST1
```

So changing on CSR4

```
CSR4(config-subif)# xconnect 192.51.100.1 1001 encapsulation mpls pw-class CUST1
% 192.51.100.1:1001 is in use
% Invalid i/f handle 0
```

Have to remove from ```int Gi5.100```

```
CSR4(config)#interface GigabitEthernet5.100                               
CSR4(config-subif)#no xconnect 192.51.100.1 1001 encapsulation mpls pw-class CUST1 
CSR4(config-subif)#interface GigabitEthernet5.101                                 
CSR4(config-subif)#xconnect 192.51.100.1 1001 encapsulation mpls pw-class CUST1   

```

Took ~30sec but became reachable

```
CUSTX-CPE1#ping vrf CUST1 10.1.1.11
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.11, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
CUSTX-CPE1#ping vrf CUST1 10.1.1.11
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.11, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
CUSTX-CPE1#ping vrf CUST1 10.1.1.11
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.11, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 2/3/4 ms
CUSTX-CPE1#ping vrf CUST1 10.1.1.11
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.11, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/3 ms
```

So that is VLAN rewrite

