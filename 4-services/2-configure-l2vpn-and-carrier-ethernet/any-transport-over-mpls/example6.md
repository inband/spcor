# example 6

QinQ

In this example SP will use QinQ.

The following assuptions will be made.

The SP PE will terminate QinQ.  As there is no switch I'll assume QinQ has been setup in the following way:

* SP has a customer facing switch that uses dot1qtunnel to encapsulate all traffic into a single vlan
* that single vlan is trunked from SP switch to PE
* both PEs and switches have same design in AS64499


However, although PE will have correct config.  CPE will not - I'll just pop traffic in QinQ on CUSTX-CPE


So for this example CUST1

* ```CSR1``` S-tag 100  C-tag 11
* ```CSR4``` S-tag 101  C-tag 11

* ```CUSTX-CPE1``` S-tag 100  C-tag 11
* ```CUSTX-CPE2``` S-tag 101  C-tag 11

CUST1-CPE1

```
hostname CUSTX-CPE1

!         
interface GigabitEthernet2.100
 encapsulation dot1Q 100 second-dot1q 11
 vrf forwarding CUST1
 ip address 10.1.1.10 255.255.255.254
!
```


CSR1

```
hostname CSR1
!
interface GigabitEthernet5.100
 encapsulation dot1Q 100 second-dot1q 11
 xconnect 192.51.100.4 10011 encapsulation mpls pw-class CUST1
!
```


CSR4
```
!
interface GigabitEthernet5.101
 encapsulation dot1Q 101 second-dot1q 11
 xconnect 192.51.100.1 10011 encapsulation mpls pw-class CUST1
!




```


CUST1-CPE2
```
hostname CUSTX-CPE1

!         
interface GigabitEthernet2.101
 encapsulation dot1Q 101 second-dot1q 11
 vrf forwarding CUST1
 ip address 10.1.1.11 255.255.255.254
!
```

Check VC status

```
CSR1#show mpls l2transport vc 10011

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5.100        Eth VLAN 100/11            192.51.100.4    10011      UP      
```

Test reachability via **Ping**

```
CUSTX-CPE1#ping vrf CUST1 10.1.1.11
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.11, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 2/2/3 ms
```


That is pretty cool and it may be worth testing again with VLAN rewrite for the inner tag. 


What I'd really like to test is the following scenario:

2 different CUSTs connect to same PE using QinQ and have a **xconnect** between inner tag.

I'll try that in the next example


