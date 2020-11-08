# Load balance

There are 2 equal cost links to ```P2``` from ```P1```


```
root@VMX1:P1> show route 172.16.0.2 

inet.0: 27 destinations, 27 routes (27 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.16.0.2/32      *[IS-IS/18] 00:05:40, metric 20
                      to 10.0.0.7 via ge-0/0/6.27
                    > to 10.0.0.25 via ge-0/0/7.227
```

But only using 

```
root@VMX1:P1> traceroute 172.16.0.2 interface lo0.2 no-resolve 
traceroute to 172.16.0.2 (172.16.0.2), 30 hops max, 52 byte packets
 1  10.0.0.25  919.968 ms  1022.008 ms *

```

Verify in FIB, note ```Type ucst```

```
root@VMX1:P1> show route forwarding-table destination 172.16.0.2 
Logical system: P1
Routing table: default.inet
Internet:
Enabled protocols: Bridging, Dual VLAN, 
Destination        Type RtRef Next hop           Type Index    NhRef Netif
172.16.0.2/32      user     0 10.0.0.25          ucst      860     5 ge-0/0/7.227

```

This ```ucst``` decision is random and is default behauvior for Juniper vMX.  The vMX will **not** load balance by default.

(Cisco XRv is load balancing egress by default) 

Configure policy.  Another interesting aside is that to do per-flow load balancing you have to select ```per-packet```.  vMX does **not** do per-packet but for historical reasons this is the option when setting up per-flow load balancing.

```
root@VMX1:P1# set policy-options policy-statement LB then load-balance ?            
Possible completions:
  consistent-hash      Give a prefix consistent load-balancing
  destination-ip-only  Give a destination based ip load-balancing
  per-packet           Load balance on a per-packet basis
  random               Load balance using packet random spray
  source-ip-only       Give a source based ip load-balancing
```

Configure policy and apply

```
root@VMX1:P1# set policy-options policy-statement LB then load-balance per-packet   
root@VMX1:P1# set routing-options forwarding-table export LB  

```

show | compare

```
root@VMX1:P1# show | compare 
[edit logical-systems P1]
+  policy-options {
+      policy-statement LB {
+          then {
+              load-balance per-packet;
+          }
+      }
+  }
+  routing-options {
+      forwarding-table {
+          export LB;
+      }
+  }
```

-------------------


Verify.  Note the ```Type``` is a new data-structure ```ulst``` - unicast list


```
root@VMX1:P1> show route forwarding-table destination 172.16.0.2    
Logical system: P1
Routing table: default.inet
Internet:
Enabled protocols: Bridging, Dual VLAN, 
Destination        Type RtRef Next hop           Type Index    NhRef Netif
172.16.0.2/32      user     0                    ulst  1048574     6
                              10.0.0.7           ucst      919     4 ge-0/0/6.27
                              10.0.0.25          ucst      860     4 ge-0/0/7.227

Logical system: P1
Routing table: __master.anon__.inet
Internet:
Enabled protocols: Bridging, Dual VLAN, 
Destination        Type RtRef Next hop           Type Index    NhRef Netif
default            perm     0                    rjct      810     1

```

The RIB still shows 1 best path

```
root@VMX1:P1> show route 172.16.0.2                                 

inet.0: 27 destinations, 27 routes (27 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.16.0.2/32      *[IS-IS/18] 00:18:44, metric 20
                      to 10.0.0.7 via ge-0/0/6.27
                    > to 10.0.0.25 via ge-0/0/7.227
```


And traceroute isn't behaving how I'd expect which makes me question how the vMX is hashing flows.

```
root@VMX1:P1> show route 172.16.0.2                                 

inet.0: 27 destinations, 27 routes (27 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.16.0.2/32      *[IS-IS/18] 00:18:44, metric 20
                      to 10.0.0.7 via ge-0/0/6.27
                    > to 10.0.0.25 via ge-0/0/7.227

root@VMX1:P1> show route 172.16.0.22                              

inet.0: 27 destinations, 27 routes (27 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.16.0.22/32     *[IS-IS/18] 00:19:39, metric 30
                      to 10.0.0.7 via ge-0/0/6.27
                    > to 10.0.0.25 via ge-0/0/7.227

root@VMX1:P1> traceroute 172.16.0.22 interface lo0.2 no-resolve   
traceroute to 172.16.0.22 (172.16.0.22), 30 hops max, 52 byte packets
 1  10.0.0.7  1476.943 ms  37.970 ms  1090.771 ms
 2  10.0.0.4  904.771 ms  123.036 ms *

root@VMX1:P1> traceroute 172.16.0.44 interface lo0.2 no-resolve    
traceroute to 172.16.0.44 (172.16.0.44), 30 hops max, 52 byte packets
 1  10.0.0.7  1064.229 ms  21.307 ms  88.557 ms
 2  10.0.0.11  1054.756 ms^[[A *  55.005 ms

```

All traceroutes are choosing **vlan 27** link.  

Anyway, the Base ISP Topology is now configure for AS65000 - time to start the book.
