# LDP Bindings

vMX by default appears to only binding for /32 loopbacks and /32 IGP. 

```
root@VMX1:PE1> show ldp database    
Input label database, 172.16.0.11:0--172.16.0.1:0
Labels received: 8
  Label     Prefix
      3      172.16.0.1/32
 299792      172.16.0.2/32
 299888      172.16.0.11/32
 299840      172.16.0.22/32
 299856      172.16.0.33/32
 299824      172.16.0.44/32
 299872      172.16.0.201/32
 299776      172.16.0.202/32
```


```
root@VMX1:PE1> show route table inet.3                      

inet.3: 7 destinations, 7 routes (7 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.16.0.1/32      *[LDP/9] 6d 01:53:09, metric 10
                    > to 10.0.0.3 via lt-0/0/0.12
172.16.0.2/32      *[LDP/9] 6d 01:53:09, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299792
172.16.0.22/32     *[LDP/9] 6d 01:53:09, metric 40
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299840
172.16.0.33/32     *[LDP/9] 6d 01:53:09, metric 20
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299856
172.16.0.44/32     *[LDP/9] 6d 01:53:09, metric 40
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299824
172.16.0.201/32    *[LDP/9] 6d 01:53:09, metric 20
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299872
172.16.0.202/32    *[LDP/9] 6d 01:53:09, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299776

```

```
root@VMX1:PE1> show route table inet.0 match-prefix 172.16.0.*      

inet.0: 34 destinations, 41 routes (34 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.16.0.1/32      *[IS-IS/15] 1w2d 01:08:06, metric 10
                    > to 10.0.0.3 via lt-0/0/0.12
172.16.0.2/32      *[IS-IS/18] 1w1d 01:55:47, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12
172.16.0.11/32     *[Direct/0] 1w2d 01:55:48
                    > via lo0.1
172.16.0.22/32     *[IS-IS/18] 1w1d 01:47:41, metric 40
                    > to 10.0.0.3 via lt-0/0/0.12
172.16.0.33/32     *[IS-IS/15] 1w1d 19:19:59, metric 20
                    > to 10.0.0.3 via lt-0/0/0.12
172.16.0.44/32     *[IS-IS/18] 1w1d 01:55:37, metric 40
                    > to 10.0.0.3 via lt-0/0/0.12
172.16.0.201/32    *[IS-IS/15] 1w1d 17:50:50, metric 20
                    > to 10.0.0.3 via lt-0/0/0.12
172.16.0.202/32    *[IS-IS/18] 1w1d 01:53:48, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12
```


-----------------------------

XRv has to be filtered otherwise there is a 1-to-1 label allocation for IGP prefixes found in the default route table.

Host routes are IGP routes with /32



XRv

```
mpls ldp
 address-family ipv4
  label
   local
    allocate for host-routes
   !
  !
 !  
```


```
RP/0/RP0/CPU0:PE2#show mpls ldp bindings 
Tue Nov 17 02:21:18.012 UTC

172.16.0.1/32, rev 49
        Local binding: label: 24010
        Remote bindings: (2 peers)
            Peer                Label    
            -----------------   ---------
            172.16.0.2:0        24000   
            172.16.0.11:0       299776  
172.16.0.2/32, rev 31
        Local binding: label: 24000
        Remote bindings: (2 peers)
            Peer                Label    
            -----------------   ---------
            172.16.0.2:0        ImpNull 
            172.16.0.11:0       299808  
172.16.0.11/32, rev 52
        Local binding: label: 24009
        Remote bindings: (2 peers)
            Peer                Label    
            -----------------   ---------
            172.16.0.2:0        24007   
            172.16.0.11:0       ImpNull 
172.16.0.22/32, rev 2
        Local binding: label: ImpNull
        Remote bindings: (2 peers)
            Peer                Label    
            -----------------   ---------
            172.16.0.2:0        24013   
            172.16.0.11:0       299856  
172.16.0.33/32, rev 50
        Local binding: label: 24011
        Remote bindings: (2 peers)
            Peer                Label    
            -----------------   ---------
            172.16.0.2:0        24008   
            172.16.0.11:0       299872  
172.16.0.44/32, rev 38
        Local binding: label: 24007
        Remote bindings: (2 peers)
            Peer                Label    
            -----------------   ---------
            172.16.0.2:0        24009   
            172.16.0.11:0       299840  
172.16.0.201/32, rev 51
        Local binding: label: 24008
        Remote bindings: (2 peers)
            Peer                Label    
            -----------------   ---------
            172.16.0.2:0        24001   
            172.16.0.11:0       299888  
172.16.0.202/32, rev 37
        Local binding: label: 24006
        Remote bindings: (2 peers)
            Peer                Label    
            -----------------   ---------
            172.16.0.2:0        24010   
            172.16.0.11:0       299792  

RP/0/RP0/CPU0:PE2#
```

Detail

```
RP/0/RP0/CPU0:PE2#show mpls ldp bindings detail 
Tue Nov 17 02:22:03.497 UTC

Advertisement Spec: None

Local Label Allocation Spec: 
        Host routes only

172.16.0.1/32, rev 49, ELC
        Local binding: label: 24010
          Advertised to: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
          Acked by: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
        Remote bindings: (2 peers)
            Peer                Label       Stale   ELC  
            -----------------   ---------   -----   -----
            172.16.0.2:0        24000        N       Y  
            172.16.0.11:0       299776       N       Y  
172.16.0.2/32, rev 31
        Local binding: label: 24000
          Advertised to: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
          Acked by: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
        Remote bindings: (2 peers)
            Peer                Label       Stale   ELC  
            -----------------   ---------   -----   -----
            172.16.0.2:0        ImpNull      N       N  
            172.16.0.11:0       299808       N       N  
172.16.0.11/32, rev 52, ELC
        Local binding: label: 24009
          Advertised to: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
          Acked by: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
        Remote bindings: (2 peers)
            Peer                Label       Stale   ELC  
            -----------------   ---------   -----   -----
            172.16.0.2:0        24007        N       Y  
            172.16.0.11:0       ImpNull      N       Y  
172.16.0.22/32, rev 2
        Local binding: label: ImpNull
          Advertised to: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
          Acked by: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
        Remote bindings: (2 peers)
            Peer                Label       Stale   ELC  
            -----------------   ---------   -----   -----
            172.16.0.2:0        24013        N       N  
            172.16.0.11:0       299856       N       N  
172.16.0.33/32, rev 50, ELC
        Local binding: label: 24011
          Advertised to: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
          Acked by: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
        Remote bindings: (2 peers)
            Peer                Label       Stale   ELC  
            -----------------   ---------   -----   -----
            172.16.0.2:0        24008        N       Y  
            172.16.0.11:0       299872       N       Y  
172.16.0.44/32, rev 38
        Local binding: label: 24007
          Advertised to: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
          Acked by: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
        Remote bindings: (2 peers)
            Peer                Label       Stale   ELC  
            -----------------   ---------   -----   -----
            172.16.0.2:0        24009        N       N  
            172.16.0.11:0       299840       N       N  
172.16.0.201/32, rev 51, ELC
        Local binding: label: 24008
          Advertised to: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
          Acked by: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
        Remote bindings: (2 peers)
            Peer                Label       Stale   ELC  
            -----------------   ---------   -----   -----
            172.16.0.2:0        24001        N       Y  
            172.16.0.11:0       299888       N       Y  
172.16.0.202/32, rev 37
        Local binding: label: 24006
          Advertised to: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
          Acked by: (2 peers)
            172.16.0.2:0       172.16.0.11:0      
        Remote bindings: (2 peers)
            Peer                Label       Stale   ELC  
            -----------------   ---------   -----   -----
            172.16.0.2:0        24010        N       N  
            172.16.0.11:0       299792       N       N  
```
