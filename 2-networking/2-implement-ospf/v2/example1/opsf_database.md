# ospf database



```
CSR1#show ip ospf 1 database        

            OSPF Router with ID (192.51.100.1) (Process ID 1)

		Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
192.51.100.1    192.51.100.1    638         0x80000004 0x000562 3
192.51.100.2    192.51.100.2    564         0x80000008 0x00FA65 3
```

CSR1 perspective 

```
CSR1#show ip ospf 1 database router self-originate 

            OSPF Router with ID (192.51.100.1) (Process ID 1)

		Router Link States (Area 0)

  LS age: 674
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.51.100.1
  Advertising Router: 192.51.100.1
  LS Seq Number: 80000004
  Checksum: 0x562
  Length: 60
  Number of Links: 3

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.51.100.1
     (Link Data) Network Mask: 255.255.255.255
      Number of MTID metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.51.100.2
     (Link Data) Router Interface address: 192.51.100.200
      Number of MTID metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.51.100.200
     (Link Data) Network Mask: 255.255.255.254
      Number of MTID metrics: 0
       TOS 0 Metrics: 1
```


CSR2 perspective (command issued on CSR1) 

```
CSR1#show ip ospf 1 database router 192.51.100.2

            OSPF Router with ID (192.51.100.1) (Process ID 1)

		Router Link States (Area 0)

  LS age: 647
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.51.100.2
  Advertising Router: 192.51.100.2
  LS Seq Number: 80000008
  Checksum: 0xFA65
  Length: 60
  Number of Links: 3

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.51.100.2
     (Link Data) Network Mask: 255.255.255.255
      Number of MTID metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.51.100.1
     (Link Data) Router Interface address: 192.51.100.201
      Number of MTID metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.51.100.200
     (Link Data) Network Mask: 255.255.255.254
      Number of MTID metrics: 0
       TOS 0 Metrics: 1


```
