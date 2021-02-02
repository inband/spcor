# Embedded Packet Capture

Platform: csr1000v


```
csr8#monitor capture TEST ?         
  WORD           Name of the Capture
  access-list    access-list to be attached 
  buffer         Buffer options
  class-map      class name to attached 
  clear          Clear Buffer
  control-plane  Control Plane 
  export         Export Buffer
  interface      Interface
  limit          Limit Packets Captured
  match          Describe filters inline
  start          Enable Capture
  stop           Disable Capture 
```

Create capture named ```TEST``` with inline filter.  ```any``` will ```Capture all packets```

```
csr8#monitor capture TEST match any

csr8#show monitor capture          

Status Information for Capture TEST
  Target Type: 
   Interface unattached 
   Status : Inactive
  Filter Details: 
    Capture all packets
  Buffer Details: 
   Buffer Type: LINEAR (default)
  Limit Details: 
   Number of Packets to capture: 0 (no limit)
   Packet Capture duration: 0 (no limit)
   Packet Size to capture: 0 (no limit)
   Packet sampling rate: 0 (no sampling)
```

Attach Interface and specify direction

```
csr8#monitor capture TEST interface GigabitEthernet 1 ?
  both  Inbound and outbound packets
  in    Inbound packets
  out   Outbound packets

csr8#monitor capture TEST interface GigabitEthernet 1 in
```

```
csr8#show monitor capture                               

Status Information for Capture TEST
  Target Type: 
 Interface: GigabitEthernet1, Direction: IN
   Status : Inactive
  Filter Details: 
    Capture all packets
  Buffer Details: 
   Buffer Type: LINEAR (default)
  Limit Details: 
   Number of Packets to capture: 0 (no limit)
   Packet Capture duration: 0 (no limit)
   Packet Size to capture: 0 (no limit)
   Packet sampling rate: 0 (no sampling)
```

Start capture

```
csr8#monitor capture TEST start
```

------

**Views**

Stats

```
csr8#show monitor capture TEST buffer brief   
```

Summary

```
csr8#show monitor capture TEST buffer brief   
```

Detail

```
csr8#show monitor capture TEST buffer detail   
```

RAW

```
csr8#show monitor capture TEST buffer dump
```

-------

Stop capture

```
csr8#monitor capture TEST stop
```
