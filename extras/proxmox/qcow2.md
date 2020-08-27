
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
