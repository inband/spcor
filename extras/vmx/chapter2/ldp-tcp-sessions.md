# LDP TCP sessions

vMX

```
root@VMX1:P1> show ldp session 
  Address                           State       Connection  Hold time  Adv. Mode
172.16.0.2                          Operational Open          26         DU
172.16.0.11                         Operational Open          24         DU
172.16.0.33                         Operational Open          24         DU
172.16.0.201                        Operational Open          24         DU
172.16.0.202                        Operational Open          28         DU
```

Session detail

```
root@VMX1:P1> show ldp session 172.16.0.2 detail 
Address: 172.16.0.2, State: Operational, Connection: Open, Hold time: 27
  Session ID: 172.16.0.1:0--172.16.0.2:0
  Next keepalive in 6 seconds
  Passive, Maximum PDU: 4096, Hold time: 30, Neighbor count: 2
  Neighbor types: discovered
  Keepalive interval: 10, Connect retry interval: 1
  Local address: 172.16.0.1, Remote address: 172.16.0.2
  Up for 1d 04:52:02
  Capabilities advertised: none
  Capabilities received: p2mp
  Protection: disabled
  Session flags: none
  Local - Restart: disabled, Helper mode: enabled
  Remote - Restart: disabled, Helper mode: disabled
  Local maximum neighbor reconnect time: 120000 msec
  Local maximum neighbor recovery time: 240000 msec
  Local Label Advertisement mode: Downstream unsolicited
  Remote Label Advertisement mode: Downstream unsolicited
  Negotiated Label Advertisement mode: Downstream unsolicited
  MTU discovery: disabled
  Nonstop routing state: Not in sync
  Next-hop addresses received:
    172.16.0.2
    10.0.0.5
    10.0.0.18
    10.0.0.7
    10.0.0.22
    10.0.0.10
    10.0.0.25

```

XRv

```
RP/0/RP0/CPU0:P2#show mpls ldp neighbor brief 
Thu Nov 12 05:03:43.788 UTC

Peer               GR  NSR  Up Time     Discovery   Addresses     Labels    
                                        ipv4  ipv6  ipv4  ipv6  ipv4   ipv6 
-----------------  --  ---  ----------  ----------  ----------  ------------
172.16.0.44:0      N   N    1d04h       1     0     4     0     23     0    
172.16.0.22:0      N   N    1d04h       1     0     5     0     24     0    
172.16.0.202:0     N   N    1d04h       1     0     4     0     22     0    
172.16.0.1:0       N   N    1d04h       2     0     6     0     11     0    
172.16.0.201:0     N   N    1d04h       1     0     3     0     11     0  
```

Session detail

```
RP/0/RP0/CPU0:P2#show mpls ldp neighbor 172.16.0.44
Thu Nov 12 05:08:16.014 UTC

Peer LDP Identifier: 172.16.0.44:0
  TCP connection: 172.16.0.44:53343 - 172.16.0.2:646
  Graceful Restart: No
  Session Holdtime: 180 sec
  State: Oper; Msgs sent/rcvd: 2020/2015; Downstream-Unsolicited
  Up time: 1d05h
  LDP Discovery Sources:
    IPv4: (1)
      GigabitEthernet0/0/0/5.78
    IPv6: (0)
  Addresses bound to this peer:
    IPv4: (4)
      10.0.0.11      10.0.0.13      10.2.0.44      172.16.0.44    
    IPv6: (0)

```
