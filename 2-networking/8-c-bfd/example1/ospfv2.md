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


Configure bfd on CSR2

```
CSR2(config)#router ospf 1
CSR2(config-router)#bfd all-interfaces 


CSR2(config)#interface GigabitEthernet 3.24
CSR2(config-subif)#bfd interval 50 min_rx 50 multiplier 5
```

Verify - neighbor (CSR4) is down

```
CSR2#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.205                       4097/0          Down      Down      Gi3.24
```

What's happening on the wire ```192.51.100.204``` is CSR2

Can see CSR2 is expecting that CSR4 will be listening on UPD/3784 for control.

```
root@pve6-lab:~# tcpdump -nni fwln511i2
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on fwln511i2, link-type EN10MB (Ethernet), capture size 262144 bytes
10:52:17.912981 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24
10:52:17.913941 IP 192.51.100.205 > 192.51.100.204: ICMP 192.51.100.205 udp port 3784 unreachable, length 36
10:52:18.668956 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24
10:52:18.669742 IP 192.51.100.205 > 192.51.100.204: ICMP 192.51.100.205 udp port 3784 unreachable, length 36
10:52:19.487922 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24
10:52:19.488689 IP 192.51.100.205 > 192.51.100.204: ICMP 192.51.100.205 udp port 3784 unreachable, length 36

root@pve6-lab:~# tcpdump -nni fwln511i2 -v
tcpdump: listening on fwln511i2, link-type EN10MB (Ethernet), capture size 262144 bytes
10:54:22.279021 IP (tos 0xc0, ttl 255, id 223, offset 0, flags [none], proto UDP (17), length 52)
    192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, length: 24
	Control, State Down, Flags: [none], Diagnostic: No Diagnostic (0x00)
	Detection Timer Multiplier: 5 (5000 ms Detection time), BFD Length: 24
	My Discriminator: 0x00001001, Your Discriminator: 0x00000000
	  Desired min Tx Interval:    1000 ms
	  Required min Rx Interval:   1000 ms
	  Required min Echo Interval:   50 ms
10:54:22.279850 IP (tos 0xc0, ttl 255, id 14780, offset 0, flags [none], proto ICMP (1), length 56)
    192.51.100.205 > 192.51.100.204: ICMP 192.51.100.205 udp port 3784 unreachable, length 36
	IP (tos 0xc0, ttl 254, id 223, offset 0, flags [none], proto UDP (17), length 52)
    192.51.100.204.49152 > 192.51.100.205.3784: [|BFD]

```

Configure BFD on CSR4

```
CSR4(config)#interface GigabitEthernet 2.24
CSR4(config-subif)#bfd interval 50 min_rx 50 multiplier 5

```

Check BFD status

```
CSR2#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.205                       4097/0          Down      Down      Gi3.24

```

Not active until applied to OSPF process

```
CSR4(config)#router ospf 1
CSR4(config-router)#bfd all-interfaces 
```


Check BFD status again


```
CSR2#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.205                       4097/4097       Up        Up        Gi3.24
```


What happened on the wire

```
11:07:11.420591 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24
11:07:11.421382 IP 192.51.100.205 > 192.51.100.204: ICMP 192.51.100.205 udp port 3784 unreachable, length 36
11:07:12.368926 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24
11:07:12.369599 IP 192.51.100.205 > 192.51.100.204: ICMP 192.51.100.205 udp port 3784 unreachable, length 36
11:07:13.358199 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24
11:07:13.358817 IP 192.51.100.205.49152 > 192.51.100.204.3784: BFDv1, Control, State Init, Flags: [none], length: 24
11:07:13.359467 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Up, Flags: [none], length: 24
11:07:13.360026 IP 192.51.100.205.49152 > 192.51.100.204.3784: BFDv1, Control, State Up, Flags: [none], length: 24
11:07:13.401826 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:07:13.402194 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:07:13.404518 IP 192.51.100.205.49152 > 192.51.100.205.3785: BFD, Echo, length: 12
11:07:13.404770 IP 192.51.100.205.49152 > 192.51.100.205.3785: BFD, Echo, length: 12
11:07:13.440923 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
```

What about **debug** from CSR2 point of view

```
CSR2#debug bfd event 
CSR2#terminal monitor 
*Aug 22 01:14:50.274: BFD-DEBUG Event: V1 FSM ld:4097 handle:1 event:RX DOWN state:DOWN (0)
*Aug 22 01:14:50.275: BFD-DEBUG Event: V1 FSM ld:4097 handle:1 event:RX UP state:INIT (0)
*Aug 22 01:14:50.275: BFD-DEBUG Event: notify client(CEF) IP:192.51.100.205, ld:4097, handle:1, event:UP,  (0)
*Aug 22 01:14:50.275: BFD-DEBUG Event: notify client(OSPF) IP:192.51.100.205, ld:4097, handle:1, event:UP,  (0)
*Aug 22 01:14:50.275: BFD-DEBUG Event: notify client(CEF) IP:192.51.100.205, ld:4097, handle:1, event:UP,  (0)
```



## test OSPFv2 with BFD enabled on link


Will be quick so for this **debug** and **tcpdump** will be used.  To break the link I changed the tagging from vlan24 to vlan25 in PROXMOX for CSR4.


```
CSR2#
*Aug 22 01:18:44.136: OSPF-1 ADJ   Gi3.24: De-register neighbor 192.51.100.205 with BFD, session 1
*Aug 22 01:18:44.136: OSPF-1 ADJ   Gi3.24:   De-registration with BFD SUCCEEDED, retcode 0
*Aug 22 01:18:44.136: OSPF-1 ADJ   Gi3.24: 192.51.100.4 address 192.51.100.205 is dead
*Aug 22 01:18:44.136: OSPF-1 ADJ   Gi3.24: 192.51.100.4 address 192.51.100.205 is dead, state DOWN
*Aug 22 01:18:44.136: %OSPF-5-ADJCHG: Process 1, Nbr 192.51.100.4 on GigabitEthernet3.24 from FULL to DOWN, Neighbor Down: BFD node down
```


```
11:18:44.639599 IP 192.51.100.205.49152 > 192.51.100.204.3784: BFDv1, Control, State Up, Flags: [none], length: 24
11:18:44.645422 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.645593 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.656769 IP 192.51.100.205.49152 > 192.51.100.205.3785: BFD, Echo, length: 12
11:18:44.657215 IP 192.51.100.205.49152 > 192.51.100.205.3785: BFD, Echo, length: 12
11:18:44.693344 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.693508 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.700816 IP 192.51.100.205.49152 > 192.51.100.205.3785: BFD, Echo, length: 12
11:18:44.701014 IP 192.51.100.205.49152 > 192.51.100.205.3785: BFD, Echo, length: 12
11:18:44.741170 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.741530 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.746577 IP 192.51.100.205.49152 > 192.51.100.205.3785: BFD, Echo, length: 12
11:18:44.746807 IP 192.51.100.205.49152 > 192.51.100.205.3785: BFD, Echo, length: 12
11:18:44.782034 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.827318 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.870514 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.909820 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.952085 IP 192.51.100.204.49152 > 192.51.100.204.3785: BFD, Echo, length: 12
11:18:44.994728 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24
11:18:45.958422 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24
11:18:46.825111 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24
11:18:47.712559 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24
11:18:48.664120 IP 192.51.100.204.49152 > 192.51.100.205.3784: BFDv1, Control, State Down, Flags: [none], length: 24

```




Bring link back up

```
CSR2#
*Aug 22 01:22:05.770: OSPF-1 ADJ   Gi3.24: 2 Way Communication to 192.51.100.4, state 2WAY
*Aug 22 01:22:05.770: OSPF-1 ADJ   Gi3.24: Nbr 192.51.100.4: Prepare dbase exchange
*Aug 22 01:22:05.771: OSPF-1 ADJ   Gi3.24: Send DBD to 192.51.100.4 seq 0x1B6 opt 0x52 flag 0x7 len 32
*Aug 22 01:22:05.771: OSPF-1 ADJ   Gi3.24: Rcv DBD from 192.51.100.4 seq 0x2EF opt 0x52 flag 0x7 len 32  mtu 1500 state EXSTART
*Aug 22 01:22:05.771: OSPF-1 ADJ   Gi3.24: NBR Negotiation Done. We are the SLAVE
*Aug 22 01:22:05.771: OSPF-1 ADJ   Gi3.24: Nbr 192.51.100.4: Summary list built, size 4
*Aug 22 01:22:05.771: OSPF-1 ADJ   Gi3.24: Send DBD to 192.51.100.4 seq 0x2EF opt 0x52 flag 0x2 len 112
*Aug 22 01:22:05.772: OSPF-1 ADJ   Gi3.24: Rcv DBD from 192.51.100.4 seq 0x2F0 opt 0x52 flag 0x1 len 112  mtu 1500 state EXCHANGE
*Aug 22 01:22:05.772: OSPF-1 ADJ   Gi3.24: Exchange Done with 192.51.100.4
*Aug 22 01:22:05.772: OSPF-1 ADJ   Gi3.24: Synchronized with 192.51.100.4, state FULL
*Aug 22 01:22:05.772: %OSPF-5-ADJCHG: Process 1, Nbr 192.51.100.4 on GigabitEthernet3.24 from LOADING to FULL, Loading Done
*Aug 22 01:22:05.778: OSPF-1 ADJ   Gi3.24: Register neighbor 192.51.100.205 with BFD, session 1
*Aug 22 01:22:05.778: OSPF-1 ADJ   Gi3.24: Send DBD to 192.51.100.4 seq 0x2F0 opt 0x52 flag 0x0 len 32
*Aug 22 01:22:45.774: OSPF-1 ADJ   Gi3.24: Nbr 192.51.100.4: Clean-up dbase exchange
```
