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






