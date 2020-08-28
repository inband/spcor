
### CSRv

```
qm create 602
qm importdisk 602 /var/lib/vz/images/csr1000v.qcow2 local-lvm
```

In Proxmox 
```
Memory: 4GB (No ballooning)
Processors: 1socket 2cores

SCSI Controller: VirtIO SCSI

Change: 
Unused Disk 0: local-lvm:vm-602-disk-0
To:
Bus: VirtIO Block

Add:
Network device: VirtIO
Serial Port

Change Boot Order: Device 1: Disk 'virtio0'
```


### XRv

```
qm create 701
qm importdisk 701 /var/lib/vz/images/xrv9k.qcow2 local-lvm
```

In Proxmox 
```
Memory: 6GB (No ballooning)
Processors: 1socket 2cores

SCSI Controller: VirtIO SCSI

Change: 
Unused Disk 0: local-lvm:vm-701-disk-0
To:
Bus: VirtIO Block

Add:
Network device: VirtIO
Serial Port

Change Boot Order: Device 1: Disk 'virtio0'
```

Didn't pick up additional interfaces after install.  

Will have to come back at some stage but compared to CSRv - it using more resourses.  Stopped VM for now.

--------------------------

Change CPU to ```Type: host```

Due to issue
```
%OS-SYSMGR-3-ERROR : dp_launcher(1)
```

```
root@pve6-lab:~# qm config 701 
balloon: 0
boot: cdn
bootdisk: virtio0
cores: 2
cpu: host
memory: 8196
net0: virtio=2E:9E:F9:68:BA:EE,bridge=vmbr0,firewall=1
net1: virtio=A2:6E:10:21:A3:C2,bridge=vmbr0,firewall=1,tag=4094
net2: virtio=2A:2F:71:E2:36:7C,bridge=vmbr0,firewall=1,tag=4094
net3: virtio=E2:2F:6E:8E:C4:62,bridge=vmbr0,firewall=1,tag=70
numa: 0
scsihw: virtio-scsi-pci
serial0: socket
serial1: socket
serial2: socket
smbios1: uuid=f50d0b96-d8a5-404b-9029-0bf42c92c8b6
sockets: 1
virtio0: local-lvm:vm-701-disk-0,size=45G
vmgenid: 6b60ab14-9db1-476c-9918-b67d3d76febc

```

Cisco [requirements](https://www.cisco.com/c/en/us/td/docs/routers/virtual-routers/xrv9k-61x/general/release/notes/b-release-notes-xrv9k-614.html)

Minimum 4 NICs

* 1 for management 
* 2 are reserved
* 1 for traffic (int Gi0/0/0/0

So 3 NICs for traffic would require a total of 6 ```virtio``` NICs.  Found that a reload was required after adding NICs.

Add interfaces (depending on how many NICs added)

```
interface preconfigure GigabitEthernet0/0/0/0
```

Observe how first 3 NICs are allocated:
```
RP/0/RP0/CPU0:ios#bash
Fri Aug 28 00:48:11.858 UTC

[host:~]$ dmesg| grep eth 
[    5.821316] udevd[1264]: renamed network interface eth0 to mgmt-eth0
[    5.827091] udevd[1262]: renamed network interface eth1 to ctrl-eth0
[    5.833171] udevd[1265]: renamed network interface eth2 to host-eth0
[   20.318129] 8021q: adding VLAN 0 to HW filter on device mgmt-eth0
[   20.319661] 8021q: adding VLAN 0 to HW filter on device ctrl-eth0
[   20.320887] 8021q: adding VLAN 0 to HW filter on device host-eth0
```

NIC 4 comes up in inventory

```
RP/0/RP0/CPU0:ios#show inventory  
Fri Aug 28 00:46:26.628 UTC
NAME: "0/0", DESCR: "Cisco IOS-XRv 9000 Centralized Line Card"
PID: R-IOSXRV9000-LC-C , VID: V01, SN: C9DCD17108A

NAME: "0/0/0", DESCR: "N/A"
PID: NODE-1G-NIC-X     , VID: N/A, SN: N/A

NAME: "0/RP0", DESCR: "Cisco IOS-XRv 9000 Centralized Route Processor"
PID: R-IOSXRV9000-RP-C , VID: V01, SN: 123BD7F6B9D

NAME: "Rack 0", DESCR: "Cisco IOS-XRv 9000 Centralized Virtual Router"
PID: R-IOSXRV9000-CC   , VID: V01, SN: EBAB0944671
```



```
[host:~]$ ifconfig -a
Gi0_0_0_0 Link encap:Ethernet  HWaddr e2:2f:6e:8e:c4:62  
          [NO FLAGS]  MTU:1514  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
          
          
[host:~]$ ip addr show dev Gi0_0_0_0
9: Gi0_0_0_0: <> mtu 1514 qdisc noop state DOWN group default qlen 1000
    link/ether e2:2f:6e:8e:c4:62 brd ff:ff:ff:ff:ff:ff
    
[host:~]$ ip link show dev Gi0_0_0_0
9: Gi0_0_0_0: <> mtu 1514 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether e2:2f:6e:8e:c4:62 brd ff:ff:ff:ff:ff:ff    
```
