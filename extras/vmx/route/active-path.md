# active-path

The ```active-path``` is the best path filter for the route command.

Here is the standard output.

```
root@VMX1:PE1> show route 192.168.20.3                                       

inet.0: 34 destinations, 41 routes (34 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

192.168.20.3/32    *[BGP/170] 3d 08:51:27, MED 0, localpref 100, from 172.16.0.201
                      AS path: 65002 I, validation-state: unverified
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299856
                    [BGP/170] 3d 08:51:29, MED 0, localpref 100, from 172.16.0.202
                      AS path: 65002 I, validation-state: unverified
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299856
                    [BGP/170] 3d 08:39:33, localpref 100, from 172.16.0.201
                      AS path: 65002 I, validation-state: unverified
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299824
```

With the ```active-path``` filter

```
root@VMX1:PE1> show route 192.168.20.3 active-path 

inet.0: 34 destinations, 41 routes (34 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

192.168.20.3/32    *[BGP/170] 3d 08:51:32, MED 0, localpref 100, from 172.16.0.201
                      AS path: 65002 I, validation-state: unverified
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299856

```

And now with detail

```
root@VMX1:PE1> show route 192.168.20.3 active-path detail 

inet.0: 34 destinations, 41 routes (34 active, 0 holddown, 0 hidden)
192.168.20.3/32 (3 entries, 1 announced)
        *BGP    Preference: 170/-101
                Next hop type: Indirect, Next hop index: 0
                Address: 0xc861c30
                Next-hop reference count: 3
                Source: 172.16.0.201
                Next hop type: Router, Next hop index: 1060
                Next hop: 10.0.0.3 via lt-0/0/0.12, selected
                Label operation: Push 299856
                Label TTL action: prop-ttl
                Load balance label: Label 299856: None; 
                Label element ptr: 0xc863b40
                Label parent element ptr: 0x0
                Label element references: 1
                Label element child references: 0
                Label element lsp id: 0
                Session Id: 0x149
                Protocol next hop: 172.16.0.33
                Indirect next hop: 0xba9cb80 1048586 INH Session ID: 0x16f
                State: <Active Int Ext>
                Local AS: 65000 Peer AS: 65000
                Age: 3d 8:53:52 	Metric: 0 	Metric2: 20 
                Validation State: unverified 
                ORR Generation-ID: 0 
                Task: BGP_65000.172.16.0.201+56241
                Announcement bits (3): 2-KRT 3-BGP_RT_Background 4-Resolve tree 4 
                AS path: 65002 I  (Originator)
                Cluster list:  172.16.0.201
                Originator ID: 172.16.0.33
                Accepted
                Localpref: 100
                Router ID: 172.16.0.201
                Addpath Path ID: 2

```




