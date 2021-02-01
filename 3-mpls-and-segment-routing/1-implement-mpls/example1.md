# Example 1

AS64499

csr1 ```Lo0 192.51.100.1```

csr2 ```Lo0 192.51.100.2```

csr3 ```Lo0 192.51.100.3```

csr4 ```Lo0 192.51.100.4```


csr1 to csr2 ```vlan12```

csr1 to csr3 ```vlan13```

csr2 to csr4 ```vlan24```

csr3 to csr4 ```vlan34```

-----------

Baseline setup

igp OSPF - area 0


csr1 route table

```
csr1#show ip route 

Gateway of last resort is 0.0.0.0 to network 0.0.0.0

S*    0.0.0.0/0 is directly connected, Null0
      192.51.100.0/24 is variably subnetted, 10 subnets, 2 masks
C        192.51.100.1/32 is directly connected, Loopback0
O        192.51.100.2/32 
           [110/2] via 192.51.100.201, 00:05:11, GigabitEthernet2.12
O        192.51.100.3/32 
           [110/101] via 192.51.100.203, 00:07:14, GigabitEthernet3.13
O        192.51.100.4/32 
           [110/3] via 192.51.100.201, 00:04:21, GigabitEthernet2.12
C        192.51.100.200/31 is directly connected, GigabitEthernet2.12
L        192.51.100.200/32 is directly connected, GigabitEthernet2.12
C        192.51.100.202/31 is directly connected, GigabitEthernet3.13
L        192.51.100.202/32 is directly connected, GigabitEthernet3.13
O        192.51.100.204/31 
           [110/2] via 192.51.100.201, 00:07:42, GigabitEthernet2.12
O        192.51.100.206/31 
           [110/102] via 192.51.100.201, 00:07:14, GigabitEthernet2.12
```






