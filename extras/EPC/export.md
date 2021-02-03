# EPC export

```
csr8#monitor capture TEST export ?
  bootflash:  Location of the file
  flash:      Location of the file
  ftp:        Location of the file
  http:       Location of the file
  https:      Location of the file
  pram:       Location of the file
  rcp:        Location of the file
  scp:        Location of the file
  tftp:       Location of the file
```

Note: if in VRF then you **must** specify ```source-address```.  Use ```ip ssh```, there is no option under ```ip scp```

```
csr8(config)#ip ssh source-interface GigabitEthernet 1
```

SCP

```
csr8#monitor capture TEST export scp://user@192.168.1.182/cap1.pcap
Writing cap1.pcap 
Password:
 Sink: C0644 946 cap1.pcap
!
Exported Successfully
```

Read in tcpdump (or Wireshark)

```
Host:~ user$ tcpdump -r cap1.pcap 
reading from file cap1.pcap, link-type EN10MB (Ethernet)
```
