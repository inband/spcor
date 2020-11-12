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
