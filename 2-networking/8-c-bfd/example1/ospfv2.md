# debug


Break link between CSR2 and CSR4 and see how default OSPFv2 behaves.  To break the link I changed the tagging from vlan24 to vlan25.  

It took 40 seconds to tear the neighbor down.  Note the Dead time goes to 0.




```
CSR2#debug ip ospf 1 adj 
OSPF adjacency debugging is on for process 1

CSR2#terminal monitor 
CSR2#show ip ospf 1 neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.51.100.4      0   FULL/  -        00:00:18    192.51.100.205  GigabitEthernet3.24
192.51.100.1      0   FULL/  -        00:00:38    192.51.100.200  GigabitEthernet2.12
CSR2#show ip ospf 1 neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.51.100.4      0   FULL/  -        00:00:15    192.51.100.205  GigabitEthernet3.24
192.51.100.1      0   FULL/  -        00:00:36    192.51.100.200  GigabitEthernet2.12
CSR2#show ip ospf 1 neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.51.100.4      0   FULL/  -        00:00:09    192.51.100.205  GigabitEthernet3.24
192.51.100.1      0   FULL/  -        00:00:39    192.51.100.200  GigabitEthernet2.12
CSR2#show ip ospf 1 neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.51.100.4      0   FULL/  -        00:00:05    192.51.100.205  GigabitEthernet3.24
192.51.100.1      0   FULL/  -        00:00:36    192.51.100.200  GigabitEthernet2.12
CSR2#show ip ospf 1 neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.51.100.1      0   FULL/  -        00:00:36    192.51.100.200  GigabitEthernet2.12

CSR2#
CSR2#
CSR2# 
*Aug 21 08:19:13.921: OSPF-1 ADJ   Gi3.24: 192.51.100.4 address 192.51.100.205 is dead
*Aug 21 08:19:13.921: OSPF-1 ADJ   Gi3.24: 192.51.100.4 address 192.51.100.205 is dead, state DOWN
*Aug 21 08:19:13.921: %OSPF-5-ADJCHG: Process 1, Nbr 192.51.100.4 on GigabitEthernet3.24 from FULL to DOWN, Neighbor Down: Dead timer expired
```

Fix vlan24 and neighbor came up.

```
*Aug 21 08:19:45.935: OSPF-1 ADJ   Gi3.24: 2 Way Communication to 192.51.100.4, state 2WAY
*Aug 21 08:19:45.935: OSPF-1 ADJ   Gi3.24: Nbr 192.51.100.4: Prepare dbase exchange
*Aug 21 08:19:45.935: OSPF-1 ADJ   Gi3.24: Send DBD to 192.51.100.4 seq 0xCED opt 0x52 flag 0x7 len 32
*Aug 21 08:19:45.937: OSPF-1 ADJ   Gi3.24: Rcv DBD from 192.51.100.4 seq 0x1F14 opt 0x52 flag 0x7 len 32  mtu 1500 state EXSTART
*Aug 21 08:19:45.937: OSPF-1 ADJ   Gi3.24: NBR Negotiation Done. We are the SLAVE
*Aug 21 08:19:45.937: OSPF-1 ADJ   Gi3.24: Nbr 192.51.100.4: Summary list built, size 4
*Aug 21 08:19:45.937: OSPF-1 ADJ   Gi3.24: Send DBD to 192.51.100.4 seq 0x1F14 opt 0x52 flag 0x2 len 112
*Aug 21 08:19:45.938: OSPF-1 ADJ   Gi3.24: Rcv DBD from 192.51.100.4 seq 0x1F15 opt 0x52 flag 0x1 len 112  mtu 1500 state EXCHANGE
*Aug 21 08:19:45.938: OSPF-1 ADJ   Gi3.24: Exchange Done with 192.51.100.4
*Aug 21 08:19:45.938: OSPF-1 ADJ   Gi3.24: Synchronized with 192.51.100.4, state FULL
*Aug 21 08:19:45.938: %OSPF-5-ADJCHG: Process 1, Nbr 192.51.100.4 on GigabitEthernet3.24 from LOADING to FULL, Loading Done
*Aug 21 08:19:45.938: OSPF-1 ADJ   Gi3.24: Send DBD to 192.51.100.4 seq 0x1F15 opt 0x52 flag 0x0 len 32

```


# implementing bfd


```


```
