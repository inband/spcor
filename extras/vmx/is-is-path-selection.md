# IS-IS path selection

I'm still setting up the **Basic ISP Topology**

So far I have:

* AS65000 links configured and full reachability
* IS-IS adjacencies and default metrics

The book says:

1. ```PE1-PE2``` and ```PE3-PE4``` require metric of 100, rather than default.

2. And configure ```RRs``` with overload bit so they don't attract transit traffic.


---------------
**Task 1**

What is the default metric?

From ```PE1``` to ```PE2``` Lo0

```
root@VMX1:PE1> show route protocol isis 172.16.0.22 

inet.0: 25 destinations, 25 routes (25 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.16.0.22/32     *[IS-IS/18] 06:48:53, metric 20
                    > to 10.0.0.1 via ge-0/0/3.16
```

Directly connected metric 10 + Loopback metric 10 = 20

Traceroute

```
root@VMX1:PE1# run traceroute 172.16.0.22 
traceroute to 172.16.0.22 (172.16.0.22), 30 hops max, 52 byte packets
 1  10.0.0.1 (10.0.0.1)  146.758 ms *  567.300 ms
```


Interestly XRv has a different metric - only 10.  So does this mean the vMX is only advertising metric of 10?

```
RP/0/RP0/CPU0:PE2#show route 172.16.0.11
Sun Nov  8 07:19:21.095 UTC

Routing entry for 172.16.0.11/32
  Known via "isis 1", distance 115, metric 10, type level-2
  Installed Nov  8 00:25:40.107 for 06:53:41
  Routing Descriptor Blocks
    10.0.0.0, from 172.16.0.11, via GigabitEthernet0/0/0/0.16
      Route metric is 10
  No advertising protos. 
```

Traceroute
```
RP/0/RP0/CPU0:PE2#traceroute 172.16.0.11
Sun Nov  8 07:23:07.053 UTC

Type escape sequence to abort.
Tracing the route to 172.16.0.11

 1  172.16.0.11 26 msec  10 msec  8 msec 
```


Lets change the directly connect interface metric to 100 on both ```PE1``` and ```PE2``` and see the behaviour.



```PE2```

```
RP/0/RP0/CPU0:PE2#show running-config | begin router isis
Sun Nov  8 07:31:07.008 UTC
Building configuration...
router isis 1
 is-type level-2-only
 net 00.0000.0000.0006.00
 address-family ipv4 unicast
  metric-style wide
 !
 interface Loopback0
  address-family ipv4 unicast
  !
 !
 interface GigabitEthernet0/0/0/0.16
  address-family ipv4 unicast
   metric 100
```






-------------
**Task 2**
