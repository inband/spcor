# prefix-list

View policy on CSR1 toward CSR10

```
CSR1#show ip bgp neighbors 192.51.100.10 policy        
 Neighbor: 192.51.100.10, Address-Family: IPv4 Unicast
```

There is no policy - that is why CSR10 is receiving 6 routes

```
CSR10#show ip bgp neighbors 192.51.100.11 routes            
BGP table version is 8, local router ID is 192.0.2.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  192.51.100.0     192.51.100.11            0             0 64499 i
 *>  192.51.100.1/32  192.51.100.11            0             0 64499 ?
 *>  192.51.100.5/32  192.51.100.11            0             0 64499 ?
 r>  192.51.100.10/31 192.51.100.11            0             0 64499 ?
 *>  192.51.100.200/31
                       192.51.100.11            0             0 64499 ?
 *>  192.51.100.202/31
                       192.51.100.11            0             0 64499 ?

Total number of prefixes 6 
```



Create an **outbound policy** on CSR1 to only advertise ```192.51.100.0/24``` to CSR10

```
CSR1(config)#ip prefix-list PL_EBGP_OUT seq 10 permit 192.51.100.0/24
CSR1(config)#ip prefix-list PL_EBGP_OUT seq 1000 deny 0.0.0.0/0 le 32

CSR1(config)#router bgp 64499
CSR1(config-router)#neighbor 192.51.100.10 prefix-list PL_EBGP_OUT out 
```

Note: There was no ```clear ip bgp``` required.  As these policy changing were for the **OUTBOUND** BGP speaker the router was smart enough to update and withdraw routes as per policy.


What happened on the wire?

```
IP (tos 0xc0, ttl 1, id 12467, offset 0, flags [DF], proto TCP (6), length 169)
    192.51.100.11.179 > 192.51.100.10.22138: Flags [P.], cksum 0xd16d (correct), seq 871:1000, ack 233, win 15125, length 129: BGP
	Update Message (2), length: 27
	  Withdrawn routes: 4 bytes
	Update Message (2), length: 54
	  Origin (1), length: 1, Flags [T]: IGP
	  AS Path (2), length: 6, Flags [T]: 64499 
	  Next Hop (3), length: 4, Flags [T]: 192.51.100.11
	  Multi Exit Discriminator (4), length: 4, Flags [O]: 0
	  Updated routes:
	    192.51.100.0/24
	Update Message (2), length: 48
	  Withdrawn routes: 25 bytes
IP (tos 0xc0, ttl 1, id 29732, offset 0, flags [DF], proto TCP (6), length 40)
    192.51.100.10.22138 > 192.51.100.11.179: Flags [.], cksum 0x5fee (correct), ack 1000, win 15810, length 0

```

Show policy (CSR1)

```
CSR1#show ip bgp neighbors 192.51.100.10 policy            
 Neighbor: 192.51.100.10, Address-Family: IPv4 Unicast
 Locally configured policies:
  prefix-list PL_EBGP_OUT out

CSR1#show ip bgp neighbors 192.51.100.10 policy detail 
 Neighbor: 192.51.100.10, Address-Family: IPv4 Unicast
 Locally configured policies:
  prefix-list PL_EBGP_OUT out

 Neighbor: 192.51.100.10, Address-Family: IPv4 Unicast <detail>
 Locally configured policies:
  prefix-list PL_EBGP_OUT out
ip prefix-list PL_EBGP_OUT: 2 entries
   seq 10 permit 192.51.100.0/24
   seq 1000 deny 0.0.0.0/0 le 32

```




```
CSR10#show ip bgp neighbors 192.51.100.11 routes 
BGP table version is 13, local router ID is 192.0.2.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  192.51.100.0     192.51.100.11            0             0 64499 i

Total number of prefixes 1 
```




