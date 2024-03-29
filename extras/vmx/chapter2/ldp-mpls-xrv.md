# LDP signalling and MPLS forwarding (XRv)

```
root@VMX1:CE2> traceroute 192.168.20.4 source 192.168.10.2    
traceroute to 192.168.20.4 (192.168.20.4) from 192.168.10.2, 30 hops max, 52 byte packets
 1  10.1.0.3 (10.1.0.3)  14.939 ms  4.972 ms  5.950 ms
 2  10.0.0.5 (10.0.0.5)  16.152 ms  5.696 ms  4.678 ms
     MPLS Label=24009 CoS=0 TTL=1 S=1
 3  10.0.0.11 (10.0.0.11)  12.234 ms  4.829 ms  5.824 ms
 4  10.2.0.4 (10.2.0.4)  7.909 ms *  789.404 ms

```

Next hop is ```172.16.0.44```

```
RP/0/RP0/CPU0:PE2#show route 192.168.20.4
Sat Nov 14 09:30:54.459 UTC

Routing entry for 192.168.20.4/32
  Known via "bgp 65000", distance 200, metric 0
  Tag 65002, type internal
  Installed Nov 13 09:11:37.766 for 1d00h
  Routing Descriptor Blocks
    172.16.0.44, from 172.16.0.201
      Route metric is 0
  No advertising protos. 
```

CEF

```
RP/0/RP0/CPU0:PE2#show cef 192.168.20.4  
Sat Nov 14 09:31:51.814 UTC
192.168.20.4/32, version 92, internal 0x5000001 0x0 (ptr 0xdf13eb8) [1], 0x0 (0xe0d7c68), 0x0 (0x0)
 Updated Nov 13 09:11:37.768
 Prefix Len 32, traffic index 0, precedence n/a, priority 4
   via 172.16.0.44/32, 3 dependencies, recursive [flags 0x6000]
    path-idx 0 NHID 0x0 [0xdf14608 0x0]
    next hop 172.16.0.44/32 via 172.16.0.44/32
```

Check CEF entry for ```172.16.0.44/32```

```
RP/0/RP0/CPU0:PE2#show cef 172.16.0.44/32
Sat Nov 14 09:33:06.012 UTC
172.16.0.44/32, version 37, internal 0x1000001 0x0 (ptr 0xdf14608) [3], 0x0 (0xe0d84a8), 0xa28 (0xe4dc9a8)
 Updated Nov 11 00:06:05.036 
 remote adjacency to GigabitEthernet0/0/0/1.67
 Prefix Len 32, traffic index 0, precedence n/a, priority 3
   via 10.0.0.5/32, GigabitEthernet0/0/0/1.67, 4 dependencies, weight 0, class 0 [flags 0x0]
    path-idx 0 NHID 0x0 [0xec3a2f0 0x0]
    next hop 10.0.0.5/32
    remote adjacency
     local label 24007      labels imposed {24009}
```

```labels imposed {24009}``` matches the initial traceroute.


```
RP/0/RP0/CPU0:PE2#show mpls ldp bindings 172.16.0.44/32
Sat Nov 14 09:34:44.229 UTC
172.16.0.44/32, rev 38
        Local binding: label: 24007
        Remote bindings: (2 peers)
            Peer                Label    
            -----------------   ---------
            172.16.0.2:0        24009   
            172.16.0.11:0       299840  
```

```
RP/0/RP0/CPU0:PE2#show mpls ldp neighbor 172.16.0.2   
Sat Nov 14 09:36:55.416 UTC

Peer LDP Identifier: 172.16.0.2:0
  TCP connection: 172.16.0.2:646 - 172.16.0.22:30788
  Graceful Restart: No
  Session Holdtime: 180 sec
  State: Oper; Msgs sent/rcvd: 5619/5619; Downstream-Unsolicited
  Up time: 3d09h
  LDP Discovery Sources:
    IPv4: (1)
      GigabitEthernet0/0/0/1.67
    IPv6: (0)
  Addresses bound to this peer:
    IPv4: (7)
      10.0.0.5       10.0.0.7       10.0.0.10      10.0.0.18      
      10.0.0.22      10.0.0.25      172.16.0.2     
    IPv6: (0)
```

``` 10.0.0.5``` is the next hop as seen in CEF and RIB.

```
RP/0/RP0/CPU0:PE2#show route 172.16.0.44
Sat Nov 14 09:35:12.835 UTC

Routing entry for 172.16.0.44/32
  Known via "isis 1", distance 115, metric 30, type level-2
  Installed Nov  9 00:29:57.964 for 5d09h
  Routing Descriptor Blocks
    10.0.0.5, from 172.16.0.44, via GigabitEthernet0/0/0/1.67
      Route metric is 30
  No advertising protos. 
```

LFIB on ```PE2```

```
RP/0/RP0/CPU0:PE2#show mpls forwarding prefix 172.16.0.44/32
Sat Nov 14 09:42:39.191 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24007  24009       172.16.0.44/32     Gi0/0/0/1.67 10.0.0.5        3456  
```


LFIB on ```P2```

```
RP/0/RP0/CPU0:P2#show mpls forwarding prefix 172.16.0.44/32
Sat Nov 14 09:43:48.262 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24009  Pop         172.16.0.44/32     Gi0/0/0/5.78 10.0.0.11       1039641  
```

Label binding in ```PE4```, note ``` Local binding: label: ImpNull```

```
RP/0/RP0/CPU0:PE4#show mpls ldp bindings 172.16.0.44/32                      
Sat Nov 14 09:46:14.173 UTC
172.16.0.44/32, rev 2
        Local binding: label: ImpNull
        Remote bindings: (2 peers)
            Peer                Label    
            -----------------   ---------
            172.16.0.2:0        24009   
            172.16.0.33:0       299840 
```

The above binding will be advertised via LDP to ```PE4``` neighbors

```
RP/0/RP0/CPU0:PE4#show mpls ldp neighbor brief 
Sat Nov 14 09:47:38.631 UTC

Peer               GR  NSR  Up Time     Discovery   Addresses     Labels    
                                        ipv4  ipv6  ipv4  ipv6  ipv4   ipv6 
-----------------  --  ---  ----------  ----------  ----------  ------------
172.16.0.2:0       N   N    3d09h       1     0     7     0     22     0    
172.16.0.33:0      N   N    3d09h       1     0     2     0     11     0 
```

Verify ```implicit null``` on ```PE3```


```
root@VMX1:PE3> show ldp database session 172.16.0.44         
Input label database, 172.16.0.33:0--172.16.0.44:0
Labels received: 19
  Label     Prefix
      3      0.0.0.0/0
  24011      10.0.0.0/31
  24012      10.0.0.2/31
  24002      10.0.0.4/31
  24005      10.0.0.6/31
  24013      10.0.0.8/31
      3      10.0.0.10/31
      3      10.0.0.12/31
  24016      10.0.0.14/31
  24010      10.0.0.16/31
  24003      10.0.0.18/31
  24015      10.0.0.20/31
  24001      10.0.0.22/31
  24004      10.0.0.24/31
      3      10.2.0.0/24
  24008      172.16.0.1/32
  24000      172.16.0.2/32
  24007      172.16.0.11/32
  24017      172.16.0.22/32
  24009      172.16.0.33/32
      3      172.16.0.44/32          ################## here is implicit null
  24006      172.16.0.201/32
  24014      172.16.0.202/32



```
