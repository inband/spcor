# inet.3

LDP FEC Mapping table


What is the equivalent in Cisco?

[Ivan's Blog](https://blog.ipspace.net/2011/12/junos-day-one-mpls-behind-scenes.html)



```
root@VMX1:PE1> show route table inet.3  

inet.3: 10 destinations, 10 routes (10 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.0.0.4/31        *[LDP/9] 03:32:11, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299792
10.0.0.10/31       *[LDP/9] 03:32:11, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299792
10.0.0.22/31       *[LDP/9] 03:32:11, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299808
172.16.0.1/32      *[LDP/9] 03:32:11, metric 10
                    > to 10.0.0.3 via lt-0/0/0.12
172.16.0.2/32      *[LDP/9] 03:32:11, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299792
172.16.0.22/32     *[LDP/9] 03:32:11, metric 40
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299840
172.16.0.33/32     *[LDP/9] 03:32:11, metric 20
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299856
172.16.0.44/32     *[LDP/9] 03:32:11, metric 40
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299824
172.16.0.201/32    *[LDP/9] 03:32:11, metric 20
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299872
172.16.0.202/32    *[LDP/9] 03:32:11, metric 30
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299776
```

---------------------------------------

Next-hop and label imposition (MPLS)

```
root@VMX1:PE1> show route 192.168.20.3 active-path detail | match "next hop"         
                Next hop type: Indirect, Next hop index: 0
                Next hop type: Router, Next hop index: 1060
                Next hop: 10.0.0.3 via lt-0/0/0.12, selected
                Protocol next hop: 172.16.0.33
                Indirect next hop: 0xba9cb80 1048586 INH Session ID: 0x16f
```

In the book it says to focus on ```Protocol next hop: 172.16.0.33```.  Using this to check LFIB:


```
root@VMX1:PE1> show route table inet.3 172.16.0.33 

inet.3: 10 destinations, 10 routes (10 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

172.16.0.33/32     *[LDP/9] 3d 08:14:27, metric 20
                    > to 10.0.0.3 via lt-0/0/0.12, Push 299856
```

The FIB

```
root@VMX1:PE1> show route forwarding-table destination 192.168.20.3 
Logical system: PE1
Routing table: default.inet
Internet:
Enabled protocols: Bridging, Dual VLAN, 
Destination        Type RtRef Next hop           Type Index    NhRef Netif
192.168.20.3/32    user     0                    indr  1048586     2
                              10.0.0.3          Push 299856     1060     2 lt-0/0/0.12
```

Why ```Push 299856```?

```
root@VMX1:PE1> show ldp database 
Input label database, 172.16.0.11:0--172.16.0.1:0
Labels received: 9
  Label     Prefix
 299792      10.0.0.4/31
 299792      10.0.0.10/31
 299808      10.0.0.22/31
      3      172.16.0.1/32
 299792      172.16.0.2/32
 299888      172.16.0.11/32
 299840      172.16.0.22/32
 299856      172.16.0.33/32             ######### Input
 299824      172.16.0.44/32
 299872      172.16.0.201/32
 299776      172.16.0.202/32

```

Input label database are labels **received**.



-----------------------------------

The output label database are **local** labels


```
root@VMX1:PE1> show ldp database 
Output label database, 172.16.0.11:0--172.16.0.1:0
Labels advertised: 9
  Label     Prefix
 299808      10.0.0.4/31
 299808      10.0.0.10/31
 299824      10.0.0.22/31
 299776      172.16.0.1/32
 299808      172.16.0.2/32
      3      172.16.0.11/32       ######### Implicit Null 
 299856      172.16.0.22/32
 299872      172.16.0.33/32
 299840      172.16.0.44/32
 299888      172.16.0.201/32
 299792      172.16.0.202/32

```




