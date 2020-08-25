# example 6b

Example6b: QinQ, 2 different CUSTs, same PE


Ok this may or may not work.  The scenario is the following:


CSR4 (PE)



CUSTX-CP1 with 2 

* ```vrf CUST1```

* ```vrf CUST2```



Start config on CSR4

Create another Loopback

```
interface Loopback5
 ip address 192.51.100.5 255.255.255.255
 ip ospf 1 area 0
!
```

Create AC and configure XConnect

```
CSR4(config)#interface Gi5.102            
CSR4(config-subif)#encapsulation dot1Q 102 second-dot1q 12
CSR4(config-subif)#xconnect 192.51.100.5 101102 encapsulation mpls pw-class PW_CLASS_MLPS
%Local switching to peer address 192.51.100.5 is not supported
```

OK - that didn't work.  

If I had 2 PE in same location it would work like the last examples.
```
CUST1_CPE <-> SP_SW1 <-> SP_PE1 <----PW----> SP_PE2 <-> SP_SW1(different ports) <-> CUST2_CPE  
```

There is probably other technologies/better practices but I'll investigate further as AToM is still a new concept to me.

------------------------

OK try again with **Local Connect** 

From [blog](http://ccie-in-3-months.blogspot.com/search/label/EVC)



```
interface GigabitEthernet5
 no ip address
 negotiation auto
 no keepalive
 service instance 102 ethernet
  encapsulation dot1q 102 second-dot1q 12
  rewrite ingress tag pop 1 symmetric
 !
 service instance 201 ethernet
  encapsulation dot1q 201 second-dot1q 12
  rewrite ingress tag pop 1 symmetric
 !
```


Ok how about remove the existing subinterfaces - only 1 left

```
interface GigabitEthernet5.101
 encapsulation dot1Q 101 second-dot1q 12
 xconnect 192.51.100.1 10011 encapsulation mpls pw-class CUST1
 
CSR4(config)#no interface GigabitEthernet5.101
```

No.

```
CSR4(config)#connect LC_TEST GigabitEthernet 5 102 GigabitEthernet 6 201
*Aug 25 03:45:23.057: CMconnect LC_TEST GigabitEthernet 5 102 GigabitEthernet 6 201
%CONN: invalid segment 1
%CONN: Invalid Command

```



Different issue - am i trying to over complicate by only using 1 interface.

```
CSR4(config)#connect LC_TEST GigabitEthernet 5 102 GigabitEthernet 5 201
%CONN: invalid segment 1
%CONN: Invalid Command
```

Created another interface on CSR4 ```Gi6```

```
interface GigabitEthernet5
 no ip address
 negotiation auto
 no keepalive
 service instance 102 ethernet
  encapsulation dot1q 102 second-dot1q 12
  rewrite ingress tag pop 1 symmetric
 !
 service instance 201 ethernet
  encapsulation dot1q 201 second-dot1q 12
  rewrite ingress tag pop 1 symmetric
 !
end

CSR4(config)#
CSR4(config)#int GigabitEthernet 5
CSR4(config-if)#no  service instance 201 ethernet
CSR4(config-if)#int GigabitEthernet 6            
CSR4(config-if)#no shut
CSR4(config-if)# service instance 201 ethernet
CSR4(config-if-srv)#  encapsulation dot1q 201 second-dot1q 12
CSR4(config-if-srv)#  rewrite ingress tag pop 1 symmetric
CSR4(config-if-srv)# !
CSR4(config-if-srv)#exit
CSR4(config-if)#exit
CSR4(config)#connect LC_TEST GigabitEthernet 5 102 GigabitEthernet 6 201
%CONN: invalid segment 1
%CONN: Invalid Command

```

Same error

Debug

```
CSR4#debug conn 
Connection Manager debugging is on
CSR4#terminal monitor 
CSR4#conf t           
Enter configuration commands, one per line.  End with CNTL/Z.

CSR4(config)#connect LC_TEST GigabitEthernet 5 102 GigabitEthernet 6 201
%CONN: invalid segment 1
%CONN: Invalid Command
CSR4(config)#
*Aug 25 02:50:31.857: CM-ETHER: failed to get interface handle for GigabitEthernet5
```


__________________________________________________

[EVC](https://www.cisco.com/c/en/us/td/docs/wireless/asr_900/feature/guides/evc.html)

No **connect** config, I think the **bridge-domain** binds them.

Don't think **evc** config required but I'll test further.  I'll also test the hairpin which is what I originally set out to do.


```
hostname CSR4
!
ethernet evc EVC1
!
interface GigabitEthernet5
 no ip address
 negotiation auto
 no keepalive
 service instance 102 ethernet EVC1
  encapsulation dot1q 102 second-dot1q 12
  rewrite ingress tag pop 1 symmetric
  bridge-domain 12
 !
!
interface GigabitEthernet6
 no ip address
 negotiation auto
 service instance 201 ethernet EVC1
  encapsulation dot1q 201 second-dot1q 12
  rewrite ingress tag pop 1 symmetric
  bridge-domain 12
 !
!
```

CUSTX-CPE2

```
hostname CUSTX-CPE2
!
vrf definition CUST1
 rd 1:1
 !
 address-family ipv4
 exit-address-family
!
vrf definition CUST2
 rd 2:2
 !
 address-family ipv4
 exit-address-family
!
!
interface GigabitEthernet2.102
 encapsulation dot1Q 102 second-dot1q 12
 vrf forwarding CUST1
 ip address 10.1.12.1 255.255.255.252
!
interface GigabitEthernet3.201
 encapsulation dot1Q 201 second-dot1q 12
 vrf forwarding CUST2
 ip address 10.1.12.2 255.255.255.252
!
!

```

Reachability

```
CUSTX-CPE2#ping vrf CUST1 10.1.12.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.12.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
```






