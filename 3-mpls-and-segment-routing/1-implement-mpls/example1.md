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

```






