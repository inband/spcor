# vMX BGP

```
root@VMX1:PE1> show bgp summary                        
Groups: 3 Peers: 4 Down peers: 0
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0               
                      14          6          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.1.0.0              65001       9363       9338       0       0 2d 22:41:49 0/0/0/0              0/0/0/0
10.1.0.6              65001       9358       9331       0       0 2d 22:38:53 2/2/2/0              0/0/0/0
172.16.0.201          65000       9955       9968       0       0  3d 3:27:17 4/8/8/0              0/0/0/0
172.16.0.202          65000       8999       9877       0       0  3d 2:47:21 0/4/4/0              0/0/0/0
```


```
root@VMX1:PE1> show route advertising-protocol bgp 172.16.0.201 

inet.0: 34 destinations, 42 routes (34 active, 0 holddown, 0 hidden)
  Prefix		  Nexthop	       MED     Lclpref    AS path
* 192.168.10.1/32         Self                 1       100        65001 I
* 192.168.10.2/32         Self                         100        65001 I

```

```
root@VMX1:PE1> show route receive-protocol bgp 172.16.0.201 

inet.0: 34 destinations, 43 routes (34 active, 0 holddown, 0 hidden)
  Prefix		  Nexthop	       MED     Lclpref    AS path
* 10.1.0.2/31             172.16.0.22          0       100        ?
* 10.1.0.4/31             172.16.0.22          0       100        ?
  192.168.10.1/32         172.16.0.22          1       100        65001 I
  192.168.10.2/32         172.16.0.22                  100        65001 I
* 192.168.20.3/32         172.16.0.33          0       100        65002 I
                          172.16.0.44                  100        65002 I
* 192.168.20.4/32         172.16.0.33                  100        65002 I
                          172.16.0.44          0       100        65002 I

inet.3: 10 destinations, 10 routes (10 active, 0 holddown, 0 hidden)

iso.0: 1 destinations, 1 routes (1 active, 0 holddown, 0 hidden)

mpls.0: 13 destinations, 13 routes (13 active, 0 holddown, 0 hidden)

inet6.0: 1 destinations, 1 routes (1 active, 0 holddown, 0 hidden)


```
