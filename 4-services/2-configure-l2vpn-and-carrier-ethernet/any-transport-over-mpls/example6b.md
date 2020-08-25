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












