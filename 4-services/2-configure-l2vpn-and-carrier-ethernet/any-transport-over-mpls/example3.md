# example 3

This example will check the wire.  VC is UP.


CSR1 (PE)

```
CSR1#show mpls l2transport vc 1001        

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5.100        Eth VLAN 100               192.51.100.4    1001       UP        

```

VC 1001 labels

```
CSR1#show mpls l2transport binding 1001
  Destination Address: 192.51.100.4,VC ID: 1001
    Local Label:  25
        Cbit: 1,    VC Type: Ethernet,    GroupID: 0
        MTU: 1500,   Interface Desc: n/a
        VCCV: CC Type: CW [1], RA [2], TTL [3]
              CV Type: LSPV [2]
    Remote Label: 23
        Cbit: 1,    VC Type: Ethernet,    GroupID: 0
        MTU: 1500,   Interface Desc: n/a
        VCCV: CC Type: CW [1], RA [2], TTL [3]
              CV Type: LSPV [2]

```



CSR2 (PE)

```
CSR4#show mpls l2transport vc 1001 

Local intf     Local circuit              Dest address    VC ID      Status
-------------  -------------------------- --------------- ---------- ----------
Gi5.100        Eth VLAN 100               192.51.100.1    1001       UP        

```

Check reachability

```
CUSTX-CPE1#ping vrf CUST1 10.1.1.11
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.11, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/3 ms
```


Now what is happening on wire for **ping**?


```


```



