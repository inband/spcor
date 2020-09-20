# Test 3 - DSCP/EXP in MPLS

In the last test I raised a question.

Will matching happen for DSCP cs6 for BGP when MPLS label pushed(egress)?  Or any DSCP? How can I test this and verify?

The scenario is ```csr5``` has BGP session with ```csr8```.  When ```csr5``` sends BGP packet out to ```csr8``` a label will be pushed.

So is QoS policy honoured for IP before label pushed?

What if I match IP - I'll create an access list for BGP peer ```172.31.0.8```.

```
CSR5#show ip access-lists 
Extended IP access list TEST3
    10 permit ip host 172.31.0.5 host 172.31.0.8 log

```

```
CSR5(config-cmap)#match access-group name TEST3
% access-lists with 'log' keyword are not supported
```

Ok removed log.  Create class-map for TEST3


```
class-map match-all INTERNETWORK_CONTROL
 match dscp cs6 
class-map match-all REALTIME
 match dscp ef 
class-map match-all SIGNALLING
 match dscp cs5 
class-map match-all CLASS_TEST3
 match access-group name TEST3
!
policy-map WAN_EDGE
 class REALTIME
  priority 100
 class SIGNALLING
  priority 50
 class INTERNETWORK_CONTROL
  bandwidth 50
 class CLASS_TEST3
  priority 10
 class class-default
  fair-queue
  random-detect
  bandwidth 800
!
ip access-list extended TEST3
 permit ip host 172.31.0.5 host 172.31.0.8
```


Check service-policy stats (don't forget to over-subscribe link)

```

    Class-map: CLASS_TEST3 (match-all)  
      0 packets, 0 bytes
      5 minute offered rate 0000 bps, drop rate 0000 bps
      Match: access-group name TEST3
      Priority: 10 kbps, burst bytes 1500, b/w exceed drops: 0
      

```


But perhaps it is hitting existing cs6

```
CSR5(config)#policy-map WAN_EDGE
          
CSR5(config-pmap)#no class INTERNETWORK_CONTROL
```

No - still not matching


```
Class-map: CLASS_TEST3 (match-all)  
      0 packets, 0 bytes
      5 minute offered rate 0000 bps, drop rate 0000 bps
      Match: access-group name TEST3
      Priority: 10 kbps, burst bytes 1500, b/w exceed drops: 0

```

Ok - instead of access list - match MPLS experimental.  It appears that for the CSR cs6 is reflected to MPLS exp 6.


```
class-map match-all CLASS_TEST3
 match mpls experimental topmost 6 
!

```



That is better


```
CSR5#show policy-map interface GigabitEthernet 2 
 GigabitEthernet2 

  Service-policy output: WAN_EDGE

...
...   
...
      

    Class-map: CLASS_TEST3 (match-all)  
      7 packets, 490 bytes
      5 minute offered rate 0000 bps, drop rate 0000 bps
      Match: mpls experimental topmost 6 
      Priority: 10 kbps, burst bytes 1500, b/w exceed drops: 0
      

```


So given that out of the box -  cs6 is reflected to MPLS exp 6.

Let's shoot EF traffic down MPLS pipe and see if that is reflected as EXP vlaue too...


in test 4
