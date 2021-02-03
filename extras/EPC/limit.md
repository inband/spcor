# EPC limit

```
csr8#monitor capture TEST limit ?
  duration    Limit total duration of capture in seconds
  every       Limit capture to one in every nth packet
  packet-len  Limit the packet length to capture
  packets     Limit number of packets to capture
  pps         Limit number of packets per second to capture

```

Packet limit

```
csr8#monitor capture TEST limit packets ?
  <1-100000>  Number of Packet  : Min 1 - Max 100000
```

PPS (Packets per second)

```
csr8#monitor capture TEST limit pps ?
  <1-1000000>  Maximum number of packets per second
```

----------------

Limit to first 10 packets

```
csr8#monitor capture TEST limit packets 10

csr8#show monitor capture TEST 

Status Information for Capture TEST
  Target Type: 
 Interface: GigabitEthernet1, Direction: IN
   Status : Inactive
  Filter Details: 
   IPv4 
    Source IP:  host 8.8.8.8
    Destination IP:  any
   Protocol: udp
   Source port(s): = 53
  Buffer Details: 
   Buffer Type: LINEAR (default)
   Buffer Size (in MB): 10
  Limit Details:                                          ### LIMIT
   Number of Packets to capture: 10                       ### 
   Packet Capture duration: 0 (no limit)
   Packet Size to capture: 0 (no limit)
   Maximum number of packets to capture per second: 1000
   Packet sampling rate: 0 (no sampling)
```
