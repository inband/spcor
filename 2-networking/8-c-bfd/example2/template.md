# bfd-template


Configure BFD template globally and apply to OSPF interfaces

```
hostname CSR1
!
bfd-template single-hop BFD_TEMPLATE
 interval min-tx 50 min-rx 50 multiplier 5
!
!
interface GigabitEthernet2.12
 encapsulation dot1Q 12
 ip address 192.51.100.200 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
 bfd template BFD_TEMPLATE
!
interface GigabitEthernet3.13
 encapsulation dot1Q 13
 ip address 192.51.100.202 255.255.255.254
 ip ospf network point-to-point
 ip ospf bfd
 ip ospf 1 area 0
 bfd template BFD_TEMPLATE
!
router ospf 1
 router-id 192.51.100.1
 passive-interface default
 no passive-interface GigabitEthernet2.12
 no passive-interface GigabitEthernet3.13
!
```

Verify on CSR1

```
CSR1#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.201                          1/1          Up        Up        Gi2.12
192.51.100.203                          2/2          Up        Up        Gi3.13

```



Setup and verify on CSR2

NOTE: CSR2 to CSR4 configured in Example1  (without template)

```
CSR2#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.200                          1/1          Up        Up        Gi2.12
192.51.100.205                       4097/4097       Up        Up        Gi3.24

```

Show neighbor detail on CSR2

```
CSR2#show bfd neighbors details 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.200                          1/1          Up        Up        Gi2.12
Session state is UP and not using echo function.
Session Host: Hardware
OurAddr: 192.51.100.201 
Handle: 2
Local Diag: 0, Demand mode: 0, Poll bit: 0
MinTxInt: 50000, MinRxInt: 50000, Multiplier: 5
Received MinRxInt: 50000, Received Multiplier: 5
Holddown (hits): 0(0), Hello (hits): 50(0)
Rx Count: 18489, Rx Interval (ms) min/max/avg: 19/95/46
Tx Count: 18508, Tx Interval (ms) min/max/avg: 40/50/46
Elapsed time watermarks: 0 0 (last: 0)
Registered protocols: OSPF CEF 
Template: BFD_TEMPLATE
Uptime: 00:14:29
Last packet: Version: 1                  - Diagnostic: 0
             State bit: Up               - Demand bit: 0
             Poll bit: 0                 - Final bit: 0
             C bit: 1                                   
             Multiplier: 5               - Length: 24
             My Discr.: 1                - Your Discr.: 1
             Min tx interval: 50000      - Min rx interval: 50000
             Min Echo interval: 0       

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.205                       4097/4097       Up        Up        Gi3.24
Session state is UP and using echo function with 50 ms interval.
Session Host: Software
OurAddr: 192.51.100.204 
Handle: 1
Local Diag: 0, Demand mode: 0, Poll bit: 0
MinTxInt: 1000000, MinRxInt: 1000000, Multiplier: 5
Received MinRxInt: 1000000, Received Multiplier: 5
Holddown (hits): 0(0), Hello (hits): 1000(6063)
Rx Count: 6059, Rx Interval (ms) min/max/avg: 1/1004/876 last: 435 ms ago
Tx Count: 6064, Tx Interval (ms) min/max/avg: 1/1004/875 last: 635 ms ago
Elapsed time watermarks: 0 0 (last: 0)
Registered protocols: OSPF CEF 
Uptime: 01:28:39
Last packet: Version: 1                  - Diagnostic: 0
             State bit: Up               - Demand bit: 0
             Poll bit: 0                 - Final bit: 0
             C bit: 0                                   
             Multiplier: 5               - Length: 24
             My Discr.: 4097             - Your Discr.: 4097
             Min tx interval: 1000000    - Min rx interval: 1000000
             Min Echo interval: 50000   
CSR2#


```


Setup and verify on CSR3

```
CSR3#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.202                          2/2          Up        Up        Gi2.13
192.51.100.207                          1/1          Up        Up        Gi3.34

```

Setup and verify on CSR4

NOTE: CSR4 to CSR2 configured in Example1 (without template)

```
CSR4#show bfd neighbors 

IPv4 Sessions
NeighAddr                              LD/RD         RH/RS     State     Int
192.51.100.204                       4097/4097       Up        Up        Gi2.24
192.51.100.206                          1/1          Up        Up        Gi3.34


```








Debug example - bring up bfd between ```CSR5``` and ```CSR6```

```
CSR5#debug bfd events

CSR5(config)#interface GigabitEthernet2.56
CSR5(config-subif)#ip os
CSR5(config-subif)#ip ospf bfd
CSR5(config-subif)#
*Sep 16 01:36:01.948: BFD-HW-API: Handle 1: Timers: Tx timer 1000000 Detect timer 0
*Sep 16 01:36:01.948: BFD-HW-API: Handle 1: Flags: Poll 0 Final 0
*Sep 16 01:36:01.948: BFD-HW-API: Handle 1: Buffer: 0x20480518 0x00000001 0x00000000 0x000F4240 0x000F4240 0x00000000 size 24
*Sep 16 01:36:01.949: BFD-DEBUG EVENT: bfd_session_created, 172.31.0.201 proc:OSPF, idb:GigabitEthernet2.56 handle:1 act
*Sep 16 01:36:01.949: %BFD-6-BFD_SESS_CREATED: BFD-SYSLOG: bfd_session_created, neigh 172.31.0.201 proc:OSPF, idb:GigabitEthernet2.56 handle:1 act
*Sep 16 01:36:01.969: BFD-DEBUG EVENT: bfd_session_created, 172.31.0.201 proc:CEF, idb:GigabitEthernet2.56 handle:1 act
*Sep 16 01:36:01.997: %BFDFSM-6-BFD_SESS_UP: BFD-SYSLOG: BFD session ld:1 handle:1 is going UP
*Sep 16 01:36:01.997: BFD-HW-API: Handle 1: Timers: Tx timer 1000000 Detect timer 5000000
*Sep 16 01:36:01.997: BFD-HW-API: Handle 1: Flags: Poll 1 Final 0
*Sep 16 01:36:01.997: BFD-HW-API: Handle 1: Buffer: 0x20E80518 0x00000001 0x00000001 0x0000C350 0x0000C350 0x00000000 size 24
*Sep 16 01:36:01.997: BFD-DEBUG Event: V1 FSM ld:1 handle:1 event:RX INIT state:DOWN (0)
*Sep 16 01:36:01.998: BFD-DEBUG Event: resetting timestamps ld:1 handle:1 (0)
*Sep 16 01:36:01.998: BFD-DEBUG Event: notify client(CEF) IP:172.31.0.201, ld:1, handle:1, event:UP,  (0)
*Sep 16 01:36:01.998: BFD-DEBUG Event: notify client(OSPF) IP:172.31.0.201, ld:1, handle:1, event:UP,  (0)
*Sep 16 01:36:01.998: BFD-DEBUG Event: notify client(CEF) IP:172.31.0.201, ld:1, handle:1, event:UP,  (0)
*Sep 16 01:36:02.024: BFD-HW-API: Handle 1: Timers: Tx timer 50000 Detect timer 250000
*Sep 16 01:36:02.024: BFD-HW-API: Handle 1: Flags: Poll 0 Final 0
*Sep 16 01:36:02.024: BFD-HW-API: Handle 1: Buffer: 0x20C80518 0x00000001 0x00000001 0x0000C350 0x0000C350 0x00000000 size 24
*Sep 16 01:36:02.025: BFD-DEBUG Event: V1 FSM ld:1 handle:1 event:RX UP state:UP (0)
*Sep 16 01:36:02.051: BFD-HW-API: Handle 1: Timers: Tx timer 50000 Detect timer 250000
*Sep 16 01:36:02.051: BFD-HW-API: Handle 1: Flags: Poll 0 Final 1
*Sep 16 01:36:02.051: BFD-HW-API: Handle 1: Buffer: 0x20D80518 0x00000001 0x00000001 0x0000C350 0x0000C350 0x00000000 size 24
*Sep 16 01:36:02.051: BFD-DEBUG Event: V1 FSM ld:1 handle:1 event:RX UP state:UP (0)
*Sep 16 01:36:02.078: BFD-DEBUG Event: V1 FSM ld:1 handle:1 event:RX UP state:UP (0)

```
