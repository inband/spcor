# next-hop-self



This example shows an eBGP prefix and advertised via iBGP from CSR1 to CSR2.

```
CSR2#show ip bgp                
BGP table version is 20, local router ID is 192.51.100.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>i 192.0.2.0        192.51.100.10            0    100      0 64496 i
 r>i 192.51.100.0     192.51.100.1             0    100      0 i
 r>i 192.51.100.1/32  192.51.100.1             0    100      0 ?
 *>i 192.51.100.5/32  192.51.100.1             0    100      0 ?
 *>i 192.51.100.10/31 192.51.100.1             0    100      0 ?
 r>i 192.51.100.200/31
                       192.51.100.1             0    100      0 ?
 r>i 192.51.100.202/31
                       192.51.100.1             0    100      0 ?
CSR2#
CSR2#
CSR2#
CSR2#
CSR2#show ip bgp ne
CSR2#show ip bgp 192.0.2.0/24
BGP routing table entry for 192.0.2.0/24, version 20
Paths: (1 available, best #1, table default)
  Not advertised to any peer
  Refresh Epoch 2
  64496
    192.51.100.10 (metric 2) from 192.51.100.1 (192.51.100.5)
      Origin IGP, metric 0, localpref 100, valid, internal, best
      rx pathid: 0, tx pathid: 0x0
CSR2#show ip cef 192.0.2.0/24
192.0.2.0/24
  nexthop 192.51.100.200 GigabitEthernet2.12

CSR2#ping 192.0.2.10      
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
!!!!!
```


The reason it is working is CSR1 is redistributing connected prefix ```192.51.100.10/31```


Stop CSR1 from redistributing

```
CSR1(config)#router bgp 64499          
CSR1(config-router)#no redistribute connected 

```

So it is in the bgp table - not the next-hop ```192.51.100.10```

```
CSR2#show ip bgp                  
BGP table version is 36, local router ID is 192.51.100.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>i 192.0.2.0        192.51.100.10            0    100      0 64496 i
 r>i 192.51.100.0     192.51.100.1             0    100      0 i
```

And in the RIB - that is because of the route to ```192.51.100.0/24``` via Null0 

```
CSR2#show ip route 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

B     192.0.2.0/24 [200/0] via 192.51.100.10, 00:02:42
      192.51.100.0/24 is variably subnetted, 11 subnets, 3 masks
S        192.51.100.0/24 is directly connected, Null0

```


```
CSR2#show ip bgp 192.0.2.1
BGP routing table entry for 192.0.2.0/24, version 28
Paths: (1 available, no best path)
  Not advertised to any peer
  Refresh Epoch 3
  64496
    192.51.100.10 (inaccessible) from 192.51.100.1 (192.51.100.5)
      Origin IGP, metric 0, localpref 100, valid, internal
      rx pathid: 0, tx pathid: 0

```


Now tried ping from CSR2

```
CSR2#ping 192.0.2.10         
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)
```

```
CSR2#show ip cef 192.51.100.10
192.51.100.10/32
  nexthop 192.51.100.10 Null0
```

### configure next-hop-self

Change the next-hop when advertising from CSR1

```
CSR1(config)#router bgp 64499                           
CSR1(config-router)# neighbor 192.51.100.2 next-hop-self    

```

Verify on CSR2

Note: next-hop is now CSR1's Lo0 ```192.51.100.1```

```
CSR2#show ip bgp                  
BGP table version is 37, local router ID is 192.51.100.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>i 192.0.2.0        192.51.100.1             0    100      0 64496 i

```



```
CSR2#show ip bgp 192.0.2.10
BGP routing table entry for 192.0.2.0/24, version 37
Paths: (1 available, best #1, table default)
  Not advertised to any peer
  Refresh Epoch 1
  64496
    192.51.100.1 (metric 2) from 192.51.100.1 (192.51.100.5)
      Origin IGP, metric 0, localpref 100, valid, internal, best
      rx pathid: 0, tx pathid: 0x0

```

Ping to verify reachability

```
CSR2#ping 192.0.2.10 source Lo0   
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.0.2.10, timeout is 2 seconds:
Packet sent with a source address of 192.51.100.2 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

```


