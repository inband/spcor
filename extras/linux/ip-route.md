# ip route


Static persistant

```
vim /etc/network/interfaces
```

```
auto eth1
iface eth1 inet static
        address 203.0.113.101/29

up ip route add 203.0.113.0/24 dev eth1 via 203.0.113.97
down ip route del 203.0.113.0/24 dev eth1 via 203.0.113.97

```
