# bgp PE1

A closer look at BGP on PE1

```PE1``` has both iBGP peers and eBGP peers.


PE1 is in AS65000

```
root@VMX1:PE1> show bgp summary 
Groups: 3 Peers: 4 Down peers: 0
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0               
                      13          6          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.1.0.0              65001      29331      29244       0       0 1w2d 5:31:33 0/0/0/0              0/0/0/0
10.1.0.6              65001      29327      29237       0       0 1w2d 5:28:37 2/2/2/0              0/0/0/0
172.16.0.201          65000      29803      29874       0       0 1w2d 10:17:01 4/7/7/0              0/0/0/0
172.16.0.202          65000      27102      29783       0       0 1w2d 9:37:05 0/4/4/0              0/0/0/0
```

-------------------

Let's look at eBGP peers

* ```10.1.0.0``` is in AS65001
* local bgp transport session IP is ```10.1.0.1```
* afi/safi is inet/unicast
* no received prefixes
* 12 advertised prefixes


```
root@VMX1:PE1> show bgp neighbor 10.1.0.0    
Peer: 10.1.0.0+59112 AS 65001  Local: 10.1.0.1+179 AS 65000
  Group: eBGP-65001            Routing-Instance: master
  Forwarding routing-instance: master  
  Type: External    State: Established    Flags: <Sync>
  Last State: OpenConfirm   Last Event: RecvKeepAlive
  Last Error: None
  Export: [ PL-eBGP-65001-CE1-OUT ADV-LOOPBACK ] 
  Options: <Preference AddressFamily PeerAS Refresh>
  Address families configured: inet-unicast
  Holdtime: 90 Preference: 170
  Number of flaps: 0
  Error: 'Cease' Sent: 0 Recv: 22
  Peer ID: 192.168.10.1    Local ID: 172.16.0.11       Active Holdtime: 90
  Keepalive Interval: 30         Group index: 1    Peer index: 0    SNMP index: 2     
  I/O Session Thread: bgpio-0 State: Enabled
  BFD: disabled, down
  Local Interface: lt-0/0/0.112                     
  NLRI for restart configured on peer: inet-unicast
  NLRI advertised by peer: inet-unicast
  NLRI for this session: inet-unicast
  Peer supports Refresh capability (2)
  Stale routes from peer are kept for: 300
  Peer does not support Restarter functionality
  Restart flag received from the peer: Notification
  NLRI that restart is negotiated for: inet-unicast
  NLRI of received end-of-rib markers: inet-unicast
  NLRI of all end-of-rib markers sent: inet-unicast
  Peer does not support LLGR Restarter functionality
  Peer supports 4 byte AS extension (peer-as 65001)
  Peer does not support Addpath
  Table inet.0 Bit: 20001
    RIB State: BGP restart is complete
    Send state: in sync
    Active prefixes:              0
    Received prefixes:            0
    Accepted prefixes:            0
    Suppressed due to damping:    0
    Advertised prefixes:          12
  Last traffic (seconds): Received 900604 Sent 643583 Checked 900604
  Input messages:  Total 29337	Updates 1	Refreshes 0	Octets 557451
  Output messages: Total 29250	Updates 6	Refreshes 0	Octets 556001
  Output Queue[1]: 0            (inet.0, inet-unicast)

```

Why is there no received prefixes?  I'll have to check ```CE1```

```
root@VMX1:PE1> show route receive-protocol bgp 10.1.0.0   
```

```
root@VMX1:CE1> show bgp summary 
Groups: 2 Peers: 3 Down peers: 0
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0               
                      43         11          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
10.1.0.1              65000      29274      29359       0       0 1w2d 5:44:59 11/12/12/0           0/0/0/0
10.1.0.5              65000      26634      29356       0       0 1w2d 5:44:08 0/10/10/0            0/0/0/0
192.168.10.2          65001      29743      29745       0       0 1w2d 8:31:59 0/21/21/0            0/0/0/0
```

```CE1``` doesn't appear to be advertising.  However, sessions are up.

```
root@VMX1:CE1> show route advertising-protocol bgp 10.1.0.1 

root@VMX1:CE1> show route advertising-protocol bgp 10.1.0.5   
```
Issue is with the policy statement

```
policy-statement ADV-LOOPBACK {
    term 1 {
        from {
            protocol [ direct local ospf ];
            route-filter 192.168.10.0/32 orlonger;
        }
        then accept;
    }
}
```

```route-filter 192.168.10.0/32 orlonger``` should be ```route-filter 192.168.10.0/24 orlonger```

Fix

```
root@VMX1:CE1> configure 
Entering configuration mode

[edit]
root@VMX1:CE1# delete policy-options policy-statement ADV-LOOPBACK term 1 from route-filter 192.168.10.0/32 orlonger 

[edit]
root@VMX1:CE1# set policy-options policy-statement ADV-LOOPBACK term 1 from route-filter 192.168.10.0/24 orlonger  

[edit]
root@VMX1:CE1# commit 
commit complete
```
Check on ```CE1```

```
root@VMX1:CE1> show route advertising-protocol bgp 10.1.0.1    

inet.0: 20 destinations, 52 routes (20 active, 0 holddown, 0 hidden)
  Prefix		  Nexthop	       MED     Lclpref    AS path
* 192.168.10.1/32         Self                                    I
* 192.168.10.2/32         Self                 1      
```

Check on ```PE1```

```
root@VMX1:PE1> show route receive-protocol bgp 10.1.0.0    

inet.0: 34 destinations, 44 routes (34 active, 1 holddown, 0 hidden)
  Prefix		  Nexthop	       MED     Lclpref    AS path
* 192.168.10.1/32         10.1.0.0                                65001 I
  192.168.10.2/32         10.1.0.0             1                  65001 I
```


And here are the 12 advertised prefixes.  Need to filter ```10.1.0.2/31``` and ```10.1.0.4/31```

```
oot@VMX1:PE1> show route advertising-protocol bgp 10.1.0.0 

inet.0: 34 destinations, 42 routes (34 active, 0 holddown, 0 hidden)
  Prefix		  Nexthop	       MED     Lclpref    AS path
* 10.1.0.2/31             Self                 100                ?
* 10.1.0.4/31             Self                 100                ?
* 172.16.0.1/32           Self                 100                I
* 172.16.0.2/32           Self                 100                I
* 172.16.0.11/32          Self                 100                I
* 172.16.0.22/32          Self                 100                I
* 172.16.0.33/32          Self                 100                I
* 172.16.0.44/32          Self                 100                I
* 172.16.0.201/32         Self                 100                I
* 172.16.0.202/32         Self                 100                I
* 192.168.20.3/32         Self                 100                65002 I
* 192.168.20.4/32         Self                 100                65002 I
```



```
root@VMX1:PE1> show bgp neighbor 10.1.0.6    
Peer: 10.1.0.6+55852 AS 65001  Local: 10.1.0.7+179 AS 65000
  Group: eBGP-65001            Routing-Instance: master
  Forwarding routing-instance: master  
  Type: External    State: Established    Flags: <Sync>
  Last State: OpenConfirm   Last Event: RecvKeepAlive
  Last Error: None
  Export: [ PL-eBGP-65001-CE2-OUT ADV-LOOPBACK ] 
  Options: <Preference AddressFamily PeerAS Refresh>
  Address families configured: inet-unicast
  Holdtime: 90 Preference: 170
  Number of flaps: 0
  Error: 'Cease' Sent: 0 Recv: 23
  Peer ID: 192.168.10.2    Local ID: 172.16.0.11       Active Holdtime: 90
  Keepalive Interval: 30         Group index: 2    Peer index: 0    SNMP index: 3     
  I/O Session Thread: bgpio-0 State: Enabled
  BFD: disabled, down
  Local Interface: lt-0/0/0.122                     
  NLRI for restart configured on peer: inet-unicast
  NLRI advertised by peer: inet-unicast
  NLRI for this session: inet-unicast
  Peer supports Refresh capability (2)
  Stale routes from peer are kept for: 300
  Peer does not support Restarter functionality
  Restart flag received from the peer: Notification
  NLRI that restart is negotiated for: inet-unicast
  NLRI of received end-of-rib markers: inet-unicast
  NLRI of all end-of-rib markers sent: inet-unicast
  Peer does not support LLGR Restarter functionality
  Peer supports 4 byte AS extension (peer-as 65001)
  Peer does not support Addpath
  Table inet.0 Bit: 20002
    RIB State: BGP restart is complete
    Send state: in sync
    Active prefixes:              2
    Received prefixes:            2
    Accepted prefixes:            2
    Suppressed due to damping:    0
    Advertised prefixes:          12
  Last traffic (seconds): Received 900677 Sent 643656 Checked 900677
  Input messages:  Total 29336	Updates 3	Refreshes 0	Octets 557497
  Output messages: Total 29246	Updates 6	Refreshes 0	Octets 555925
  Output Queue[1]: 0            (inet.0, inet-unicast)

```








--------------------------------

bgp route table 

NOTE: format is very hard to read

```
root@VMX1:PE1> show route protocol bgp terse    

inet.0: 34 destinations, 44 routes (34 active, 1 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

A V Destination        P Prf   Metric 1   Metric 2  Next hop        AS path
* ? 10.1.0.2/31        B 170        100          0                  ?
  unverified                                       >10.0.0.3
  ?                    B 170        100          0                  ?
  unverified                                       >10.0.0.3
* ? 10.1.0.4/31        B 170        100          0                  ?
  unverified                                       >10.0.0.3
  ?                    B 170        100          0                  ?
  unverified                                       >10.0.0.3
* ? 192.168.10.1/32    B 170        100                             65001 I
  unverified                                       >10.1.0.0
  ?                    B 170        100                             65001 I
  unverified                                       >10.0.0.3
  ?                    B 170        100          1                  65001 I
  unverified                                       >10.1.0.6
* ? 192.168.10.2/32    B 170        100                             65001 I
  unverified                                       >10.1.0.6
  ?                    B 170        100                             65001 I
  unverified                                       >10.0.0.3
  ?                    B 170        100          1                  65001 I
  unverified                                       >10.1.0.0
* ? 192.168.20.3/32    B 170        100          0                  65002 I
  unverified                                       >10.0.0.3
  ?                    B 170        100          0                  65002 I
  unverified                                       >10.0.0.3
  ?                    B 170        100                             65002 I
  unverified                                       >10.0.0.3
* ? 192.168.20.4/32    B 170        100          0                  65002 I
  unverified                                       >10.0.0.3
  ?                    B 170        100          0                  65002 I
  unverified                                       >10.0.0.3


```

Only best/active

```
root@VMX1:PE1> show route protocol bgp active-path 

inet.0: 34 destinations, 43 routes (34 active, 1 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.1.0.2/31        *[BGP/170] 1w1d 11:29:32, MED 0, localpref 100, from 172.16.0.201
                      AS path: ?, validation-state: unverified
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299840
10.1.0.4/31        *[BGP/170] 1w1d 11:29:32, MED 0, localpref 100, from 172.16.0.201
                      AS path: ?, validation-state: unverified
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299840
192.168.10.1/32    *[BGP/170] 00:19:00, localpref 100
                      AS path: 65001 I, validation-state: unverified
                    > to 10.1.0.0 via lt-0/0/0.112
192.168.10.2/32    *[BGP/170] 1w2d 02:05:28, localpref 100
                      AS path: 65001 I, validation-state: unverified
                    > to 10.1.0.6 via lt-0/0/0.122
192.168.20.3/32    *[BGP/170] 1w0d 11:22:33, MED 0, localpref 100, from 172.16.0.201
                      AS path: 65002 I, validation-state: unverified
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299856
192.168.20.4/32    *[BGP/170] 1w0d 11:21:34, MED 0, localpref 100, from 172.16.0.201
                      AS path: 65002 I, validation-state: unverified
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299824
```



The following is a little better but format still difficult

```
root@VMX1:PE1> show route protocol bgp active-path terse 

inet.0: 34 destinations, 43 routes (34 active, 1 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

A V Destination        P Prf   Metric 1   Metric 2  Next hop        AS path
* ? 10.1.0.2/31        B 170        100          0                  ?
  unverified                                       >10.0.0.3
* ? 10.1.0.4/31        B 170        100          0                  ?
  unverified                                       >10.0.0.3
* ? 192.168.10.1/32    B 170        100                             65001 I
  unverified                                       >10.1.0.0
* ? 192.168.10.2/32    B 170        100                             65001 I
  unverified                                       >10.1.0.6
* ? 192.168.20.3/32    B 170        100          0                  65002 I
  unverified                                       >10.0.0.3
* ? 192.168.20.4/32    B 170        100          0                  65002 I
  unverified                                       >10.0.0.3
```

What is ```validation-state: unverified```?

[Origin Validation](https://www.juniper.net/documentation/en_US/junos/topics/topic-map/bgp-origin-as-validation.html)

This is based on RPKI and validating against trusted database - usually maintain by RIR - such as APNIC, RIPE 


