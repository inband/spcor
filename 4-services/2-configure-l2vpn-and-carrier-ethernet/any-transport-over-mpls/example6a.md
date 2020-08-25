# example 6a 

Same as exampe 6 but with different C-Tag

QinQ VLAN rewrite


So for this example CUST1

* ```CSR1``` S-tag 100  C-tag 11
* ```CSR4``` S-tag 101  C-tag 12

* ```CUSTX-CPE1``` S-tag 100  C-tag 11
* ```CUSTX-CPE2``` S-tag 101  C-tag 12

CUST1-CPE1

```
hostname CUSTX-CPE1

!         
interface GigabitEthernet2.100
 encapsulation dot1Q 100 second-dot1q 12
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


The follow will change from example 6.

CSR4
```
!
interface GigabitEthernet5.101
 encapsulation dot1Q 101 second-dot1q 12
 xconnect 192.51.100.1 10011 encapsulation mpls pw-class CUST1
!




```


CUST1-CPE2
```
hostname CUSTX-CPE1

!         
interface GigabitEthernet2.101
 encapsulation dot1Q 101 second-dot1q 12
 vrf forwarding CUST1
 ip address 10.1.1.11 255.255.255.254
!
```

Verify

```
CSR1#show mpls l2transport vc 10011

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5.100        Eth VLAN 100/11            192.51.100.4    10011      UP   

```

CRS4
```
CSR4#show mpls l2transport vc 10011

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5.101        Eth VLAN 101/12            192.51.100.1    10011      UP        
```



And it worked straight away

```
CUSTX-CPE2#ping vrf CUST1 10.1.1.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/3/4 ms
```



Another verification command

```
CSR1#show xconnect all detail 
Legend:    XC ST=Xconnect State  S1=Segment1 State  S2=Segment2 State
  UP=Up       DN=Down            AD=Admin Down      IA=Inactive
  SB=Standby  HS=Hot Standby     RV=Recovering      NH=No Hardware

XC ST  Segment 1                         S1 Segment 2                         S2
------+---------------------------------+--+---------------------------------+--
UP pri   ac Gi5.100:100/11(Eth VLAN)     UP mpls 192.51.100.4:10011           UP
            Interworking: ethernet               Local VC label 26              
                                                 Remote VC label 23             
                                                 pw-class: CUST1     

```
