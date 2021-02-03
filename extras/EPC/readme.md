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

Show parameters (note the ```buffer size``` and ```limit pps``` were not specified, so these are default values)

```
csr8#show monitor capture TEST parameter 
   monitor capture TEST interface GigabitEthernet1 IN
   monitor capture TEST match any
   monitor capture TEST buffer size 10
   monitor capture TEST limit pps 1000
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
*Feb  2 23:57:24.079: %BUFCAP-6-ENABLE:  
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
Stopped capture point : TEST
```


--------

Clear buffer

```
csr8#monitor capture TEST clear 
Captured data will be deleted [clear]?[confirm]
cleared buffer : TEST

*Feb  2 23:58:37.654: %BUFCAP-6-DISABLE: Capture Point TEST disabled.
```


------------

Filters

The ```match``` statement allows for inline filters.

```
csr8#monitor capture TEST match any

csr8#show monitor capture          

Status Information for Capture TEST
  Target Type: 
   Interface unattached 
   Status : Inactive
  Filter Details:                 ### FILTER
    Capture all packets           ### 
  Buffer Details: 
   Buffer Type: LINEAR (default)
  Limit Details: 
   Number of Packets to capture: 0 (no limit)
   Packet Capture duration: 0 (no limit)
   Packet Size to capture: 0 (no limit)
   Packet sampling rate: 0 (no sampling)
```

Specify new filter

```
csr8#monitor capture TEST match ipv4 any any 
A filter is already attached to the capture . Replace with specified filter?[confirm]

csr8#show monitor capture TEST

Status Information for Capture TEST
  Target Type: 
 Interface: GigabitEthernet1, Direction: IN
   Status : Inactive
  Filter Details:               ### FILTER
   IPv4                         ###
    Source IP:  any             ###
    Destination IP:  any        ###
   Protocol: any                ###
  Buffer Details: 
   Buffer Type: LINEAR (default)
   Buffer Size (in MB): 10
  Limit Details: 
   Number of Packets to capture: 0 (no limit)
   Packet Capture duration: 0 (no limit)
   Packet Size to capture: 0 (no limit)
   Maximum number of packets to capture per second: 1000
   Packet sampling rate: 0 (no sampling)
csr8#
```

Another example.  Capture IPv4 on Gig1 inbound from src 8.8.8.8 to dst 192.168.1.0/24, any protocol

```
csr8#$match ipv4 host 8.8.8.8 192.168.1.0/24                               
A filter is already attached to the capture . Replace with specified filter?[confirm]

csr8#show monitor capture TEST                                             

Status Information for Capture TEST
  Target Type: 
 Interface: GigabitEthernet1, Direction: IN
   Status : Inactive
  Filter Details:                       ###
   IPv4                                 ###
    Source IP:  host 8.8.8.8            ###
    Destination IP:  192.168.1.0/24     ###
   Protocol: any                        ###
  Buffer Details: 
   Buffer Type: LINEAR (default)
   Buffer Size (in MB): 10
  Limit Details: 
   Number of Packets to capture: 0 (no limit)
   Packet Capture duration: 0 (no limit)
   Packet Size to capture: 0 (no limit)
   Maximum number of packets to capture per second: 1000
   Packet sampling rate: 0 (no sampling)
```

Another example. Specifying UDP as protocol

```
csr8#monitor capture TEST match ipv4 protocol udp host 8.8.8.8 any 
A filter is already attached to the capture . Replace with specified filter?[confirm]
```
