# Notes 1

QoS is not a simple topic and I don't know too much about it.  I'll start with some scratch notes first.  
These notes will focus on 

* behaviour/observations
* practical/lab
* reading material - books/official documentation/blogs/etc


'QoS is only necessary when congestion is an issue.'  This statement means that if there is enough bandwidth to service all requirements then QoS does NOT have to be invoked.

However, in the Service Provider environment this may not be an option due to:

* over-subscription
* down-shifting
* mirco-bursts

Whilst total service at all times may not be feasible - it certainly makes sense to use one or more QoS policies to manage services in relation to business requirements.

For example, a common business requirement is to have voice traffic prioritised over all over traffic.

Cisco provides platforms and tools to implement QoS policies.  Key areas are:

* classification - matching, marking
* policy - policing, shaping
* queuing

Queuing is also known as congestion management.


For the rest of these notes1 I'll just focus on **classification - matching, marking**

Lab

CUST1 ```172.20.2.11``` to CSR10 ```192.0.2.10``` via CSR1


From CUSTX-CPE1

```
CUSTX-CPE1#traceroute vrf CUST1 192.0.2.10 source D1 numeric 
Type escape sequence to abort.
Tracing the route to 192.0.2.10
VRF info: (vrf in name/id, vrf out name/id)
  1 192.51.100.1 2 msec 1 msec 1 msec
  2 192.51.100.10 2 msec *  2 msec
```

This **traceroute** traverses 2 links.

**vlan100** is ```CUSTX-CPE1``` to ```CSR1```

```
root@pve6-lab:~# tcpdump -nntei vmbr0v100 -v
```


**vlan10** is ```CSR1``` to ```CSR10```

```
root@pve6-lab:~# tcpdump -nntei vmbr0v100 -v
```


This will give me the means of observing ingress & egress at CSR1 to/from CUSTX-CPE1 and CSR10.


To begin let's examine default behaviour.

From CUSTX-CPE1 -

```
CUSTX-CPE1#ping vrf CUST1 192.0.2.10 source Dialer1 tos 184  
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
Packet sent with a source address of 172.20.2.11 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
```

vlan100

```
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 130: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x18] IP (0x0021), length 102: (tos 0xb8, ttl 255, id 34917, offset 0, flags [none], proto ICMP (1), length 100)
    172.20.2.11 > 192.0.2.10: ICMP echo request, id 92, seq 0, length 80
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 130: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x18] IP (0x0021), length 102: (tos 0xb8, ttl 254, id 34917, offset 0, flags [none], proto ICMP (1), length 100)
    192.0.2.10 > 172.20.2.11: ICMP echo reply, id 92, seq 0, length 80
```

So end-to-end ToS is 184.


vlan10

```
0e:c4:7c:99:c3:2a > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 118: vlan 10, p 0, ethertype IPv4, (tos 0xb8, ttl 254, id 34922, offset 0, flags [none], proto ICMP (1), length 100)
    172.20.2.11 > 192.0.2.10: ICMP echo request, id 93, seq 0, length 80
42:c7:46:4c:5e:8d > 0e:c4:7c:99:c3:2a, ethertype 802.1Q (0x8100), length 118: vlan 10, p 0, ethertype IPv4, (tos 0xb8, ttl 255, id 34922, offset 0, flags [none], proto ICMP (1), length 100)
    192.0.2.10 > 172.20.2.11: ICMP echo reply, id 93, seq 0, length 80

```

Cisco MQC default is **trust model**. Is that what **trust model** means anyway?  Or is this just swapping the L2 header? CEF?

Well that was for ToS/DSCP(Layer 3) - let's see what happens with CoS(Layer 2)


On CUSTX-CPE1 let match ToS/DSCP and Set CoS.


So there service-policy is applied to Dialer1 - I'll try later with it applied to Gig interface.  One thing I noted is that I can specify an inner CoS value.

```
CUSTX-CPE1(config-pmap-c)#set ?
  cos            Set IEEE 802.1Q/ISL class of service/user priority
  cos-inner      Set Inner CoS
```

Anyway here is the config for test.

```
hostname CUSTX-CPE1
!
!
class-map match-all CLASS_RTP
 match dscp ef 
!
policy-map POLICY_RTP
 class CLASS_RTP
  set cos 5
!
interface Dialer1
 vrf forwarding CUST1
 service-policy output POLICY_RTP

```
Ping
```
CUSTX-CPE1#ping vrf CUST1 192.0.2.10 source Dialer1 tos 184
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
Packet sent with a source address of 172.20.2.11 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/9 ms

```


vlan 100 

```
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 130: vlan 100, p 5, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x18] IP (0x0021), length 102: (tos 0xb8, ttl 255, id 34937, offset 0, flags [none], proto ICMP (1), length 100)
    172.20.2.11 > 192.0.2.10: ICMP echo request, id 96, seq 0, length 80
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 130: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x18] IP (0x0021), length 102: (tos 0xb8, ttl 254, id 34937, offset 0, flags [none], proto ICMP (1), length 100)
    192.0.2.10 > 172.20.2.11: ICMP echo reply, id 96, seq 0, length 80
```

So CoS is only changed on the outer vlan tag egress from CUSTX-CPE1.  So now not sure what **trust model** means?  Does it mean only for **DSCP**(Layer 3)?


vlan 10 

```
0e:c4:7c:99:c3:2a > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 118: vlan 10, p 0, ethertype IPv4, 172.20.2.11 > 192.0.2.10: ICMP echo request, id 96, seq 2, length 80
42:c7:46:4c:5e:8d > 0e:c4:7c:99:c3:2a, ethertype 802.1Q (0x8100), length 118: vlan 10, p 0, ethertype IPv4, 192.0.2.10 > 172.20.2.11: ICMP echo reply, id 96, seq 2, length 80

```

No CoS vlaues retained over second link.


Next let's try ```cos-inner```.

```
policy-map POLICY_RTP
 class CLASS_RTP
  set cos 5
  set cos-inner 5
```

Same result but with inner vlan tag CoS value changed.

vlan 100

```
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 130: vlan 100, p 5, ethertype 802.1Q, vlan 1, p 5, ethertype PPPoE S, PPPoE  [ses 0x18] IP (0x0021), length 102: (tos 0xb8, ttl 255, id 34947, offset 0, flags [none], proto ICMP (1), length 100)
    172.20.2.11 > 192.0.2.10: ICMP echo request, id 98, seq 0, length 80
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 130: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x18] IP (0x0021), length 102: (tos 0xb8, ttl 254, id 34947, offset 0, flags [none], proto ICMP (1), length 100)
    192.0.2.10 > 172.20.2.11: ICMP echo reply, id 98, seq 0, length 80

```

vlan 10 not shown.




Change service-policy from ```Dialer1``` to ```Gi2.64499```

```
CUSTX-CPE1(config)#interface Dialer1
CUSTX-CPE1(config-if)#no  service-policy output POLICY_RTP
CUSTX-CPE1(config-if)#interface GigabitEthernet2.64499
CUSTX-CPE1(config-subif)# service-policy output POLICY_RTP   

CUSTX-CPE1#ping vrf CUST1 192.0.2.10 source Dialer1 tos 184
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
Packet sent with a source address of 172.20.2.11 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
```

Same result

```
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 130: vlan 100, p 5, ethertype 802.1Q, vlan 1, p 5, ethertype PPPoE S, PPPoE  [ses 0x18] IP (0x0021), length 102: (tos 0xb8, ttl 255, id 34952, offset 0, flags [none], proto ICMP (1), length 100)
    172.20.2.11 > 192.0.2.10: ICMP echo request, id 99, seq 0, length 80
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 130: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x18] IP (0x0021), length 102: (tos 0xb8, ttl 254, id 34952, offset 0, flags [none], proto ICMP (1), length 100)
    192.0.2.10 > 172.20.2.11: ICMP echo reply, id 99, seq 0, length 80
```


Next test - let's do the same egress ```CSR1``` to ```CSR10``` and see what happens.  First match on ```dscp```.  Will router even modify that layer?

```
hostname CSR1
!
class-map match-all CLASS_RTP
 match dscp ef 
!
policy-map POLICY_RTP
 class CLASS_RTP
  set cos 5
!
interface GigabitEthernet4.10
 encapsulation dot1Q 10
 vrf forwarding AS64499
 ip address 192.51.100.11 255.255.255.254
 service-policy output POLICY_RTP
```

CPE1

```
CUSTX-CPE1#ping vrf CUST1 192.0.2.10 source Dialer1 tos 184
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
Packet sent with a source address of 172.20.2.11 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
```

vlan 100

```
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 130: vlan 100, p 5, ethertype 802.1Q, vlan 1, p 5, ethertype PPPoE S, PPPoE  [ses 0x18] IP (0x0021), length 102: (tos 0xb8, ttl 255, id 34985, offset 0, flags [none], proto ICMP (1), length 100)
    172.20.2.11 > 192.0.2.10: ICMP echo request, id 105, seq 3, length 80
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 130: vlan 100, p 0, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x18] IP (0x0021), length 102: (tos 0xb8, ttl 254, id 34985, offset 0, flags [none], proto ICMP (1), length 100)
    192.0.2.10 > 172.20.2.11: ICMP echo reply, id 105, seq 3, length 80
```


vlan 10

```
0e:c4:7c:99:c3:2a > 42:c7:46:4c:5e:8d, ethertype 802.1Q (0x8100), length 118: vlan 10, p 5, ethertype IPv4, 172.20.2.11 > 192.0.2.10: ICMP echo request, id 105, seq 3, length 80
42:c7:46:4c:5e:8d > 0e:c4:7c:99:c3:2a, ethertype 802.1Q (0x8100), length 118: vlan 10, p 0, ethertype IPv4, 192.0.2.10 > 172.20.2.11: ICMP echo reply, id 105, seq 3, length 80
```


Yes - the router has inpected and modified.


Now lets try egress from ```CSR1``` to ```CPE```. Remembering that this link is ```QinQ``` and ```PPPoE```.  


```
hostname CSR1
!
class-map match-all CLASS_RTP
 match dscp ef 
!
policy-map POLICY_RTP
 class CLASS_RTP
  set cos 5
!
interface GigabitEthernet5.64499
 encapsulation dot1Q 100 second-dot1q any
 pppoe enable group BBA_GROUP_1
  service-policy output POLICY_RTP
```

Well that did NOT change CoS value(same results as last test).  Let's remove

```
CSR1(config-subif)#no  service-policy output POLICY_RTP
```

Doesn't make sense but try anyway - apply to ingress from ```CSR10```

```
CSR1(config)#interface GigabitEthernet4.10
CSR1(config-subif)# service-policy input POLICY_RTP 
Set cos not supported in input policy
```

OK.  So the question is how to set CoS value for egress in scenario where SP providing PPPoE over QinQ?


Try with the Virtual-template then the per-user policy-map (RADIUS)

```
CSR1(config)#interface Virtual-template 1
CSR1(config-if)# service-policy output POLICY_RTP
```

Clear session and wait for it to re-connect and check policy is applied.

```
CSR1#clear pppoe all

CSR1#show policy-map session 
 
 SSS session identifier 25 - 

  Service-policy output: POLICY_RTP

    Class-map: CLASS_RTP (match-all)  
      0 packets, 0 bytes
      30 second offered rate 0000 bps, drop rate 0000 bps
      Match:  dscp ef (46)
      QoS Set
        cos 5
          Marker statistics: Disabled

    Class-map: class-default (match-any)  
      0 packets, 0 bytes
      30 second offered rate 0000 bps, drop rate 0000 bps
      Match: any 

```


Ping form CPE1

```
CUSTX-CPE1#ping vrf CUST1 192.0.2.10 source Dialer1 tos 184
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
Packet sent with a source address of 172.20.2.11 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

```

Check on CSR1

```
CSR1#show policy-map session 
 
 SSS session identifier 25 - 

  Service-policy output: POLICY_RTP

    Class-map: CLASS_RTP (match-all)  
      5 packets, 650 bytes
      30 second offered rate 0000 bps, drop rate 0000 bps
      Match:  dscp ef (46)
      QoS Set
        cos 5
          Marker statistics: Disabled

    Class-map: class-default (match-any)  
      22 packets, 924 bytes
      30 second offered rate 0000 bps, drop rate 0000 bps
      Match: any 

```



vlan 100

```
a2:df:d1:ed:e9:d5 > 4a:c7:d4:1b:83:a1, ethertype 802.1Q (0x8100), length 130: vlan 100, p 5, ethertype 802.1Q, vlan 1, p 5, ethertype PPPoE S, PPPoE  [ses 0x19] IP (0x0021), length 102: (tos 0xb8, ttl 255, id 35001, offset 0, flags [none], proto ICMP (1), length 100)
    172.20.2.11 > 192.0.2.10: ICMP echo request, id 108, seq 4, length 80
4a:c7:d4:1b:83:a1 > a2:df:d1:ed:e9:d5, ethertype 802.1Q (0x8100), length 130: vlan 100, p 5, ethertype 802.1Q, vlan 1, p 0, ethertype PPPoE S, PPPoE  [ses 0x19] IP (0x0021), length 102: (tos 0xb8, ttl 254, id 35001, offset 0, flags [none], proto ICMP (1), length 100)
    192.0.2.10 > 172.20.2.11: ICMP echo reply, id 108, seq 4, length 80
```


That worked (only outer tag as inner tag not specified yet).  However- this is far from elegant.

The better method is per-user policy-map assigned via RADIUS reply.  







