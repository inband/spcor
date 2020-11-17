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


