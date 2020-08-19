# neighbor adjacency


Look at CSR1.  There are 2 neighbor adjacencies.

```
CSR1#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.51.100.3      0   FULL/  -        00:00:38    192.51.100.203  GigabitEthernet3.13
192.51.100.2      0   FULL/  -        00:00:31    192.51.100.201  GigabitEthernet2.12
```

NeighborID - **OSPF router id** of the neigbor

Pri - priority, used for shared link adjacencies where a router will be selected as Designated Router(DR) or Backup(BDR)

State - state of adjacency

Dead Time - by default "dead time" is 40 seconds and counts down.  Everytime there is a keepalive it will reset to 40 seconds

Address - the neighbors IP address with in adjacency link

Interface - this routers(self) interface to reach neighbor



Look at CSR1s neighbor adjacency to CSR1

```
CSR1#show ip ospf neighbor 192.51.100.2
 Neighbor 192.51.100.2, interface address 192.51.100.201
    In the area 0 via interface GigabitEthernet2.12
    Neighbor priority is 0, State is FULL, 30 state changes
    DR is 0.0.0.0 BDR is 0.0.0.0
    Options is 0x12 in Hello (E-bit, L-bit)
    Options is 0x52 in DBD (E-bit, L-bit, O-bit)
    LLS Options is 0x1 (LR)
    Dead timer due in 00:00:37
    Neighbor is up for 2d16h   
    Index 1/1, retransmission queue length 0, number of retransmission 2
    First 0x0(0)/0x0(0) Next 0x0(0)/0x0(0)
    Last retransmission scan length is 1, maximum is 1
    Last retransmission scan time is 0 msec, maximum is 0 msec
```

This is a point-to-point adjacency - note that both DR and BDR values are 0.


Now a look at the Link-state Database (LSDB) from CSR1 perspective

```
CSR1#show ip ospf database 

            OSPF Router with ID (192.51.100.1) (Process ID 1)

		Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
192.51.100.1    192.51.100.1    164         0x8000007D 0x001716 5
192.51.100.2    192.51.100.2    1487        0x80000081 0x009B85 5
192.51.100.3    192.51.100.3    1574        0x80000054 0x004102 5
192.51.100.4    192.51.100.4    471         0x80000056 0x0042F6 5
```

If you have a look on CSR2 the LSDB should be the same.

```
CSR2#show ip ospf 1 database 

            OSPF Router with ID (192.51.100.2) (Process ID 1)

		Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
192.51.100.1    192.51.100.1    298         0x8000007D 0x001716 5
192.51.100.2    192.51.100.2    1619        0x80000081 0x009B85 5
192.51.100.3    192.51.100.3    1708        0x80000054 0x004102 5
192.51.100.4    192.51.100.4    603         0x80000056 0x0042F6 5
```




