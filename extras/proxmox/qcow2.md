
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
net1: virtio=A2:6E:10:21:A3:C2,bridge=vmbr0,firewall=1,tag=71
net2: virtio=2A:2F:71:E2:36:7C,bridge=vmbr0,firewall=1,tag=72
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



Add interfaces

```
interface preconfigure GigabitEthernet0/0/0/1
interface preconfigure GigabitEthernet0/0/0/2
```
