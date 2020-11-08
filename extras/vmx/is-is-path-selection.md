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

```PE1```

```
root@VMX1:PE1# set protocols isis interface ge-0/0/3.16 level 2 metric 100  
```

Now ```PE2``` prefers link to ```P2```

```
RP/0/RP0/CPU0:PE2#show route 172.16.0.11
Sun Nov  8 07:48:46.216 UTC

Routing entry for 172.16.0.11/32
  Known via "isis 1", distance 115, metric 30, type level-2
  Installed Nov  8 07:47:22.685 for 00:01:24
  Routing Descriptor Blocks
    10.0.0.5, from 172.16.0.1, via GigabitEthernet0/0/0/1.67
      Route metric is 30
  No advertising protos. 
```
Traceroute

```
RP/0/RP0/CPU0:PE2#traceroute 172.16.0.11
Sun Nov  8 07:49:36.079 UTC

Type escape sequence to abort.
Tracing the route to 172.16.0.11

 1  10.0.0.5 72 msec  41 msec  28 msec 
 2  10.0.0.6 1847 msec 
    10.0.0.24 116 msec 
    10.0.0.6 125 msec 
 3  172.16.0.11 170 msec  150 msec  121 msec 
```



Ok now for ```PE2```

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

Route

```
root@VMX1:PE1> show route 172.16.0.22 

inet.0: 25 destinations, 26 routes (25 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.16.0.22/32     *[IS-IS/18] 00:03:25, metric 40
                    > to 10.0.0.3 via lt-0/0/0.12
```



Traceroute - OK but looks like vMX doesn't EC load balance by default - this cant be fix later on ```P1```

```
root@VMX1:PE1> traceroute 172.16.0.22    
traceroute to 172.16.0.22 (172.16.0.22), 30 hops max, 52 byte packets
 1  10.0.0.3 (10.0.0.3)  3142.048 ms  1734.468 ms  328.063 ms
 2  10.0.0.25 (10.0.0.25)  163.612 ms  51.124 ms  16.674 ms
 3  10.0.0.4 (10.0.0.4)  23.654 ms *  166.483 ms
```

```PE3``` and ```PE4```

```
root@VMX1# set logical-systems PE3 protocols isis interface ge-0/0/9.38 level 2 metric 100 

[edit]
root@VMX1# commit 
commit complete
```

Verify
```
root@VMX1:PE3> show route 172.16.0.44 

inet.0: 22 destinations, 23 routes (22 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.16.0.44/32     *[IS-IS/18] 00:02:16, metric 40
                    > to 10.0.0.8 via lt-0/0/0.32
```
Traceroute

```
root@VMX1:PE3> traceroute 172.16.0.44 interface lo0.3 no-resolve 
traceroute to 172.16.0.44 (172.16.0.44), 30 hops max, 52 byte packets
 1  10.0.0.8  3629.922 ms  3316.192 ms  466.354 ms
 2  10.0.0.7  151.919 ms  145.312 ms  41.384 ms
 3  10.0.0.11  139.903 ms *  22.941 ms
```



```
RP/0/RP0/CPU0:PE4(config-isis)#interface GigabitEthernet0/0/0/0.38
RP/0/RP0/CPU0:PE4(config-isis-if)#address-family ipv4 unicast 
RP/0/RP0/CPU0:PE4(config-isis-if-af)#metric 100
RP/0/RP0/CPU0:PE4(config-isis-if-af)#commit
```
Verify

```
RP/0/RP0/CPU0:PE4#show route 172.16.0.33
Sun Nov  8 07:56:52.436 UTC

Routing entry for 172.16.0.33/32
  Known via "isis 1", distance 115, metric 30, type level-2
  Installed Nov  8 07:56:27.743 for 00:00:26
  Routing Descriptor Blocks
    10.0.0.10, from 172.16.0.1, via GigabitEthernet0/0/0/1.78
      Route metric is 30
  No advertising protos. 
```

Traceroute

```
RP/0/RP0/CPU0:PE4#traceroute 172.16.0.33 source Loopback 0 numeric 
Sun Nov  8 07:58:22.892 UTC

Type escape sequence to abort.
Tracing the route to 172.16.0.33

 1  10.0.0.10 373 msec  11 msec  4 msec 
 2  10.0.0.6 6 msec  4 msec 
    10.0.0.24 4 msec 
 3  172.16.0.33 5 msec  5 msec  6 msec 
```


-------------
**Task 2**
