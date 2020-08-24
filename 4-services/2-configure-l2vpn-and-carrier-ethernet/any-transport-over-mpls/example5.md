# example 5

Example5: Trunk


* CUST1-CP1 will encapsulation traffic in multiple VLANs

* CUST1-CP2 will encapsulation traffic in multiple VLANs



CUST1-CPE1

```
hostname CUSTX-CPE1
!         
interface GigabitEthernet2.100
 encapsulation dot1Q 100
 vrf forwarding CUST1
 ip address 10.1.1.10 255.255.255.254
!
!         
interface GigabitEthernet2.101
 encapsulation dot1Q 101
 vrf forwarding CUST1
 ip address 10.1.1.12 255.255.255.254
!
```

CUST1-CPE2

```
hostname CUSTX-CPE2
!
interface GigabitEthernet2.100
 encapsulation dot1Q 100
 vrf forwarding CUST1
 ip address 10.1.1.11 255.255.255.254
!
interface GigabitEthernet2.101
 encapsulation dot1Q 101
 vrf forwarding CUST1
 ip address 10.1.1.13 255.255.255.254
!
```


CSR1
```
hostname CSR1

!
no interface GigabitEthernet5.100
interface GigabitEthernet5
 xconnect 192.51.100.4 1001 encapsulation mpls pw-class CUST1
```

CSR4
```
hostname CSR4
!
no interface GigabitEthernet5.101

interface GigabitEthernet5
 xconnect 192.51.100.1 1001 encapsulation mpls pw-class CUST1
```


Verify

```
CSR1#show mpls l2transport vc 1001

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5            Ethernet                   192.51.100.4    1001       UP        

CSR1#
```



Ok so there was a reachability issue. Everything looked OK but could not ping from ```CUSTX-CPE1``` to ```CUSTX-CPE2```

I could see ARP broadcast from ```CUSTX-CPE1``` 

* ingress to ```CUSTX-CPE1``` OK
* ingress to ```CSR4``` OK
* NO egress from ```CSR4``` to ```CUSTX-CPE2```



Needed to go to one of PE and clear **targeted ldp neighbor**

Possibly due to re-configuration of existing VC ID.

```
CSR4#clear mpls ldp neighbor ?
  *        Clear all neighbors
  A.B.C.D  IP address for LDP neighbor
  vrf      VRF Routing/Forwarding instance information

CSR4#clear mpls ldp neighbor 192.51.100.1
```

Tested reachability. OK

```
CUSTX-CPE1#ping vrf CUST1 10.1.1.13
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.13, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 2/2/4 ms
CUSTX-CPE1#ping vrf CUST1 10.1.1.11
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.11, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 2/2/3 ms
CUSTX-CPE1#
```



