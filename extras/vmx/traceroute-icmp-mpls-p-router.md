# traceroute icmp tunnelling & mpls P router

The issue is the traceroute behaviour for Basic ISP topology.

```
root@VMX1:CE1> traceroute 192.168.20.3 source 192.168.10.1 
traceroute to 192.168.20.3 (192.168.20.3) from 192.168.10.1, 30 hops max, 52 byte packets
 1  10.1.0.1 (10.1.0.1)  78.453 ms  4.710 ms  4.429 ms
 2  * * *
 3  10.0.0.9 (10.0.0.9)  4.077 ms  5.135 ms  3.808 ms
 4  10.2.0.3 (10.2.0.3)  6.464 ms *  152.705 ms
```

Hop 2 is ```P1```.  ```P1``` is a provider router.  Default Junos behaviour is dropping the ICMP unreachable - TTL expired. 

On ```P1```

```
root@VMX1:P1# set protocols mpls i
                                  ^
'i' is ambiguous.
Possible completions:
  icmp-tunneling       Allow MPLS LSPs to be used for tunneling ICMP error packets
> interface            MPLS interface options
  ipv6-tunneling       Allow MPLS LSPs to be used for tunneling IPv6 traffic
[edit]
root@VMX1:P1# set protocols mpls icmp-tunneling 

[edit]
root@VMX1:P1# commit 
```

-----------------
Back to ```CE1``` to verify

```
root@VMX1:CE1> traceroute 192.168.20.3 source 192.168.10.1    
traceroute to 192.168.20.3 (192.168.20.3) from 192.168.10.1, 30 hops max, 52 byte packets
 1  10.1.0.1 (10.1.0.1)  2195.880 ms  958.825 ms  155.682 ms
 2  10.0.0.3 (10.0.0.3)  129.692 ms  123.497 ms  32.883 ms
     MPLS Label=299856 CoS=0 TTL=1 S=1
 3  10.0.0.9 (10.0.0.9)  3.959 ms  3.546 ms  3.490 ms
 4  10.2.0.3 (10.2.0.3)  6.681 ms *  72.886 ms

```
