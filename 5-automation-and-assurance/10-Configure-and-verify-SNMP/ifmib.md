# ifmib information

I have been using Elastiflow for netflow information - unfortunately the **interfaces** are listed by mib **index**.

```
CSR2#show snmp mib ifmib ifindex 
%SNMP agent not enabled

CSR2#show udp 
Proto        Remote      Port      Local       Port  In Out  Stat TTY OutputIF
CSR1#
```

Need to enable snmp

```
CSR2(config)#snmp-server source-interface informs Lo0

CSR2#show udp        
Proto        Remote      Port      Local       Port  In Out  Stat TTY OutputIF
 17       --listen--          192.51.100.2      161   0   0 2001001   0 
 17       --listen--          192.51.100.2      162   0   0 2001011   0 
 17       --listen--          192.51.100.2    54473   0   0 2001011   0 
 17(v6)   --listen--          --any--           161   0   0 2020001   0 
 17(v6)   --listen--          --any--           162   0   0 2020011   0 
 17(v6)   --listen--          --any--         53400   0   0 2020001   0 
```

Now can see ifmib ifindices

```
CSR2#show snmp mib ifmib ifindex          
GigabitEthernet1: Ifindex = 1
GigabitEthernet2.1: Ifindex = 5
GigabitEthernet3: Ifindex = 3
GigabitEthernet2.12: Ifindex = 7
Loopback0: Ifindex = 8
Null0: Ifindex = 4
GigabitEthernet3.24: Ifindex = 9
GigabitEthernet2.2: Ifindex = 6
GigabitEthernet2: Ifindex = 2
```



Interesting - investigate further

```
CSR2(config)#snmp ifmib ifindex ?
  persist  Persist interface indices

```

The above should mean that ifindices do not change.

