# neighbor adjacency


Look at CSR1.  There are 2 neighbor adjacencies.

```
CSR1#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.51.100.3      0   FULL/  -        00:00:38    192.51.100.203  GigabitEthernet3.13
192.51.100.2      0   FULL/  -        00:00:31    192.51.100.201  GigabitEthernet2.12
```

NeighborID - **OSPF router id** of the neigbor.
Pri - priority, used for shared link adjacencies where a router will be selected as Designated Router(DR) or Backup(BDR)
State - state of adjacency.
Dead Time - by default "dead time" is 40 seconds and counts down.  Everytime there is a keepalive it will reset to 40 seconds. 
Address - the neighbors IP address with in adjacency link.
Interface - this routers interface to reach neighbor.
