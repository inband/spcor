# update

Add prefix to CSR1

```
router bgp 64499
 bgp log-neighbor-changes
 network 192.51.100.0
 neighbor 192.51.100.2 remote-as 64499
 neighbor 192.51.100.2 update-source Loopback0`

ip route 192.51.100.0 255.255.255.0 Null0
```

From CSR1

```
CSR1#show ip bgp neighbors 192.51.100.2 advertised-routes 
BGP table version is 2, local router ID is 192.51.100.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  192.51.100.0     0.0.0.0                  0         32768 i
```

From CSR2

```
CSR2#show ip bgp summary 
BGP router identifier 192.51.100.2, local AS number 64499
BGP table version is 2, main routing table version 2
1 network entries using 248 bytes of memory
1 path entries using 120 bytes of memory
1/1 BGP path/bestpath attribute entries using 248 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 616 total bytes of memory
BGP activity 1/0 prefixes, 1/0 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
192.51.100.1    4        64499      17      17        2    0    0 00:11:43        1
```

```
CSR2#show ip bgp neighbor 192.51.100.1 routes 
BGP table version is 2, local router ID is 192.51.100.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>i 192.51.100.0     192.51.100.1             0    100      0 i

Total number of prefixes 1
```




####

```
IP (tos 0xc0, ttl 255, id 7920, offset 0, flags [DF], proto TCP (6), length 95)
    192.51.100.1.179 > 192.51.100.2.45962: Flags [P.], cksum 0xf2eb (correct), seq 38:93, ack 39, win 16095, length 55: BGP
	Update Message (2), length: 55
	  Origin (1), length: 1, Flags [T]: IGP
	    0x0000:  00
	  AS Path (2), length: 0, Flags [T]: empty
	  Next Hop (3), length: 4, Flags [T]: 192.51.100.1
	    0x0000:  c033 6401
	  Multi Exit Discriminator (4), length: 4, Flags [O]: 0
	    0x0000:  0000 0000
	  Local Preference (5), length: 4, Flags [T]: 100
	    0x0000:  0000 0064
	  Updated routes:
	    192.51.100.0/24
IP (tos 0xc0, ttl 255, id 6313, offset 0, flags [DF], proto TCP (6), length 40)
    192.51.100.2.45962 > 192.51.100.1.179: Flags [.], cksum 0x5ee0 (correct), seq 39, ack 93, win 16040, length 0

```

Now add same route to CSR2

```
ip route 192.51.100.0 255.255.255.0 Null0
```

```
CSR2#show ip bgp
BGP table version is 5, local router ID is 192.51.100.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 r>i 192.51.100.0     192.51.100.1             0    100      0 i


CSR2#show ip bgp neighbor 192.51.100.1 routes 
BGP table version is 3, local router ID is 192.51.100.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 r>i 192.51.100.0     192.51.100.1             0    100      0 i

Total number of prefixes 1 

CSR2#show ip bgp rib-failure 
  Network            Next Hop                      RIB-failure   RIB-NH Matches
192.51.100.0       192.51.100.1        Higher admin distance              n/a
```


Now add **network statement** to CSR2

```
router bgp 64499
 bgp log-neighbor-changes
 network 192.51.100.0
 neighbor 192.51.100.1 remote-as 64499
 neighbor 192.51.100.1 update-source Loopback0
```


```
IP (tos 0xc0, ttl 255, id 6335, offset 0, flags [DF], proto TCP (6), length 95)
    192.51.100.2.45962 > 192.51.100.1.179: Flags [P.], cksum 0xf11a (correct), seq 248:303, ack 283, win 15850, length 55: BGP
	Update Message (2), length: 55
	  Origin (1), length: 1, Flags [T]: IGP
	    0x0000:  00
	  AS Path (2), length: 0, Flags [T]: empty
	  Next Hop (3), length: 4, Flags [T]: 192.51.100.2
	    0x0000:  c033 6402
	  Multi Exit Discriminator (4), length: 4, Flags [O]: 0
	    0x0000:  0000 0000
	  Local Preference (5), length: 4, Flags [T]: 100
	    0x0000:  0000 0064
	  Updated routes:
	    192.51.100.0/24
IP (tos 0xc0, ttl 255, id 7942, offset 0, flags [DF], proto TCP (6), length 40)
    192.51.100.1.179 > 192.51.100.2.45962: Flags [.], cksum 0x5deb (correct), seq 283, ack 303, win 15831, length 0
```

No longer RIB failure

```
CSR2#show ip bgp             
BGP table version is 4, local router ID is 192.51.100.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  192.51.100.0     0.0.0.0                  0         32768 i
 * i                  192.51.100.1             0    100      0 i

CSR2#show ip bgp neighbor 192.51.100.1 routes 
BGP table version is 4, local router ID is 192.51.100.2
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 * i 192.51.100.0     192.51.100.1             0    100      0 i

Total number of prefixes 1 

CSR2#show ip bgp rib-failure                  
  Network            Next Hop                      RIB-failure   RIB-NH Matches
```



