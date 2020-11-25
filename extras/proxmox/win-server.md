# Win Server



```
root@pve6:~# qm config 500
balloon: 0
boot: cdn
bootdisk: ide0
cores: 2
ide0: local-lvm:vm-500-disk-1,size=40G
ide2: none,media=cdrom
memory: 32768
name: win-server
net0: e1000=76:C3:A2:DE:66:16,bridge=vmbr0,firewall=1,tag=222
numa: 0
onboot: 1
ostype: l26
smbios1: uuid=898b32ee-1719-4c02-8a0c-b60042be7d6f
sockets: 1
vmgenid: 894cb455-c9ee-49c6-87ed-47b749434f6b
```
