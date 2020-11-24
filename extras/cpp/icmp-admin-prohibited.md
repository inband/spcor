# icmp unreachable - admin prohibited filter


Here is the scenario.

* ACL is used at PROVIDER EDGE to block traffic from source to an inbound host or PE itself.
* the interface receiving traffic(and checking ACL) is NOT configured with ```no ip unreachables```
* if ACL is matched an icmp type 3 code 13 **admin prohibited filter** is sent back to the source

Instead of this behaviour I would like to silently drop traffic - while keeping ```ip unreachables``` configuration on inteface



The test is to ping from CE1 ```192.168.10.1``` to BR3 ```192.168.20.3```

On ```BR3``` an ACL is added

```
hostname BR3
!
interface GigabitEthernet2.1234
 ip access-group ACL_EDGE_IN in
! 
ip access-list extended ACL_EDGE_IN
 permit tcp host 10.2.0.33 eq bgp host 10.2.0.3
 permit tcp host 10.2.0.33 host 10.2.0.3 eq bgp
 permit icmp host 10.2.0.33 host 10.2.0.3
 deny   ip any host 192.168.20.3
 permit ip any host 192.168.20.3
 permit ip any host 192.168.20.4
```

Verify

```
BR3#show ip access-lists ACL_EDGE_IN
Extended IP access list ACL_EDGE_IN
    10 permit tcp host 10.2.0.33 eq bgp host 10.2.0.3 (586 matches)
    20 permit tcp host 10.2.0.33 host 10.2.0.3 eq bgp
    30 permit icmp host 10.2.0.33 host 10.2.0.3
    401 deny ip any host 192.168.20.3 (3614 matches)
```

However the icmp type 3 code 13 is generated and sent from ```BR3```

```
10:42:19.209186 3e:ea:b0:4c:3f:b0 > fa:c0:e6:83:ab:3e, ethertype 802.1Q (0x8100), length 74: vlan 3412, p 0, ethertype IPv4, 10.2.34.3 > 192.168.10.1: ICMP host 192.168.20.3 unreachable - admin prohibited filter, length 36
10:42:20.179069 3e:ea:b0:4c:3f:b0 > fa:c0:e6:83:ab:3e, ethertype 802.1Q (0x8100), length 74: vlan 3412, p 0, ethertype IPv4, 10.2.34.3 > 192.168.10.1: ICMP host 192.168.20.3 unreachable - admin prohibited filter, length 36
10:42:21.180112 3e:ea:b0:4c:3f:b0 > fa:c0:e6:83:ab:3e, ethertype 802.1Q (0x8100), length 74: vlan 3412, p 0, ethertype IPv4, 10.2.34.3 > 192.168.10.1: ICMP host 192.168.20.3 unreachable - admin prohibited filter, length 36
10:42:22.184732 3e:ea:b0:4c:3f:b0 > fa:c0:e6:83:ab:3e, ethertype 802.1Q (0x8100), length 74: vlan 3412, p 0, ethertype IPv4, 10.2.34.3 > 192.168.10.1: ICMP host 192.168.20.3 unreachable - admin prohibited filter, length 36
```

This is what it looks like from ```CE1```

```
root@VMX1:CE1> ping 192.168.20.3 interface lo0.21          
PING 192.168.20.3 (192.168.20.3): 56 data bytes
36 bytes from 10.2.34.3: Communication prohibited by filter
Vr HL TOS  Len   ID Flg  off TTL Pro  cks      Src      Dst
 4  5  00 0054 3037   0 0000  3c  01 af1d 192.168.10.1  192.168.20.3 

36 bytes from 10.2.34.3: Communication prohibited by filter
Vr HL TOS  Len   ID Flg  off TTL Pro  cks      Src      Dst
 4  5  00 0054 3074   0 0000  3c  01 aee0 192.168.10.1  192.168.20.3 

36 bytes from 10.2.34.3: Communication prohibited by filter
Vr HL TOS  Len   ID Flg  off TTL Pro  cks      Src      Dst
 4  5  00 0054 30af   0 0000  3c  01 aea5 192.168.10.1  192.168.20.3 

36 bytes from 10.2.34.3: Communication prohibited by filter
Vr HL TOS  Len   ID Flg  off TTL Pro  cks      Src      Dst
 4  5  00 0054 30f4   0 0000  3c  01 ae60 192.168.10.1  192.168.20.3 
```

So what if an ACL is attached to outgoing interface to:

```
deny icmp any any administratively-prohibited
```

It does not match - the above is not matched. Why exactly?  Does it have something to do with iot being locally generated traffic rather than routed(transit) traffic?

----------------------

Control Plane Policing

[Policing and Shaping](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_plcshp/configuration/xe-3s/qos-plcshp-xe-3s-book/qos-plcshp-ctrl-pln-plc.html)

There was no straight-out ```drop``` option - so ```police``` with ```conform-action drop  exceed-action drop  violate-action drop ``` is used.


```
hostname BR3
!
!
class-map match-all icmp-class
 match access-group 141
!
policy-map control-plane-out
 class icmp-class
  police cir 8000 bc 1000 be 1000 conform-action drop  exceed-action drop  violate-action drop 
!
control-plane
 service-policy output control-plane-out
!
```



Verify


```
BR3(config-ext-nacl)#do show policy-map control-plane                   
 Control Plane 

  Service-policy output: control-plane-out

    Class-map: icmp-class (match-all)  
      142 packets, 7952 bytes
      5 minute offered rate 0000 bps, drop rate 0000 bps
      Match: access-group 141
      police:
          cir 8000 bps, bc 1000 bytes, be 1000 bytes
        conformed 142 packets, 7952 bytes; actions:
          drop 
        exceeded 0 packets, 0 bytes; actions:
          drop 
        violated 0 packets, 0 bytes; actions:
          drop 
        conformed 0000 bps, exceeded 0000 bps, violated 0000 bps

    Class-map: class-default (match-any)  
      19003 packets, 55872758 bytes
      5 minute offered rate 135000 bps, drop rate 0000 bps
      Match: any 
```

```CE1``` is not receiving anything - which is the desired behaviour.

```
root@VMX1:CE1> ping 192.168.20.3 interface lo0.21    
PING 192.168.20.3 (192.168.20.3): 56 data bytes



```

