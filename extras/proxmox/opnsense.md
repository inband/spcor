# OPNsense 

Testing in PROXMOX

* size can be reduced.

```
root@pve6-lab:~# qm config 500
bootdisk: scsi0
cores: 2
ide2: none,media=cdrom
memory: 2048
name: lap-opnsense
net0: e1000=66:A0:6A:DD:C2:ED,bridge=vmbr0,firewall=1
net1: e1000=AA:89:2C:57:89:B9,bridge=vmbr0,firewall=1,tag=555
numa: 0
ostype: l26
scsi0: local-lvm:vm-500-disk-0,size=32G
scsihw: virtio-scsi-pci
smbios1: uuid=78c9b16e-150a-45b9-898d-b1557245708e
sockets: 1
vmgenid: de089b35-d27b-437d-8718-f8ed98e84f1f
```
