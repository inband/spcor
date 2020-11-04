# vmx

This was a pain.  Hopefully I have most of it documented below.  Surely it is going to give me a headache next time too. 

The goal is to create an Ubuntu host and run vMX on it.

Installed Ubuntu Server 18.04

```
root@pve:~# cat /etc/pve/qemu-server/700.conf 
balloon: 0
bootdisk: scsi0
cores: 4
cpu: host,flags=+pdpe1gb
ide2: none,media=cdrom
memory: 24576
name: ubunu18
net0: virtio=3E:FA:0B:2B:78:D3,bridge=vmbr0,firewall=1,tag=999
net1: virtio=F2:49:EF:9C:6C:C6,bridge=vmbr0,firewall=1,tag=222
net2: virtio=C2:44:0D:E1:6C:94,bridge=vmbr0,firewall=1,tag=1712
net3: virtio=CA:A0:92:73:F9:8B,bridge=vmbr0,firewall=1
net4: virtio=86:6C:30:08:A8:56,bridge=vmbr0,firewall=1
numa: 1
onboot: 1
ostype: l26
scsi0: local-lvm:vm-700-disk-0,size=40G
scsihw: virtio-scsi-pci
smbios1: uuid=60c8be82-765f-4c7d-90f6-e03ddd11923d
sockets: 1
vmgenid: 49881a7b-b7bf-461a-89a5-8adb44eb4932
```

PVE host requires nesting enabled

```
cat /sys/module/kvm_intel/parameters/nested  
echo "options kvm-intel nested=Y" > /etc/modprobe.d/kvm-intel.conf
shutdown -r now
```

Enable hugepages

```
root@ubuntu18:~# cat /etc/default/grub
# If you change this file, run 'update-grub' afterwards to update
# /boot/grub/grub.cfg.
# For full documentation of the options in this file, see:
#   info -f grub -n 'Simple configuration'

GRUB_DEFAULT=0
GRUB_TIMEOUT_STYLE=hidden
GRUB_TIMEOUT=0
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="maybe-ubiquity quiet intel_iommu=on hugepagesz=1G default_hugepagesz=1G hugepages=16"
```

```
update-grub
shutdown -r now
```

Disable KSM

```
root@ubuntu18:~# cat /etc/default/qemu-kvm 
# Set to 1 to enable KSM, 0 to disable KSM, and AUTO to use default settings.
# After changing this setting restart the qemu-kvm service.
#KSM_ENABLED=AUTO
KSM_ENABLED=0
SLEEP_MILLISECS=200

# Dropped VHOST_NET_ENABLED as this is auto-loaded in recent kernels
# it still works, but is deprecated and will be removed in a later version

# Dropped KVM_HUGEPAGES as systemd provides feasible hugepage moutpoints
# it still works, but is deprecated and will be removed in a later version
```

Install prequisites

```
apt install qemu-kvm
apt install libvirt-bin
apt install bridge-utils
apt install libyaml-dev
apt install libnuma-dev
apt install libparted0-dev
apt install libpciaccess-dev
apt install libyajl-dev
apt install libxml2-dev
apt install libglib2.0-dev
apt install libnl-3-dev
apt install net-tools
apt install python-yaml
apt install numactl
apt install python-netifaces
apt install python-dev
apt install tree
```

Nest virt

```
lsmod |grep kvm
modprobe kvm_intel
```

```
root@ubuntu18:~# cat /etc/modprobe.d/qemu-system-x86.conf
options kvm_intel nested=1 enable_apicv=0
```

May need a reboot. 

Copy ```scp``` 
```
cd /root/
scp -r host:/images/vmx .
```

Storage permissions

```
root@ubuntu18:/root/vmx# cat /etc/libvirt/qemu.conf | grep -C10 'user ='
# specified as a user name or as a user id. The qemu driver will try to
# parse this value first as a name and then, if the name doesn't exist,
# as a user id.
#
# Since a sequence of digits is a valid user name, a leading plus sign
# can be used to ensure that a user id will not be interpreted as a user
# name.
#
# Some examples of valid values are:
#
#       user = "qemu"   # A user named "qemu"
#       user = "+0"     # Super user (uid=0)
#       user = "100"    # A user named "100" or a user with uid=100
#
user = "root"
user = "libvirt-qemu"

# The group for QEMU processes run by the system instance. It can be
# specified in a similar way to user.
group = "root"
```

```
root@ubuntu18:/root/vmx# cat config/vmx.conf 
##############################################################
#
#  vmx.conf
#  Config file for vmx on the hypervisor.
#  Uses YAML syntax. 
#  Leave a space after ":" to specify the parameter value.
#
##############################################################

--- 
#Configuration on the host side - management interface, VM images etc.
HOST:
    identifier                : vmx1   # Maximum 6 characters
    host-management-interface : ens18
    routing-engine-image      : "/root/vmx/images/junos-vmx-x86-64-18.2R1.9.qcow2"
    routing-engine-hdd        : "/root/vmx/images/vmxhdd.img"
    forwarding-engine-image   : "/root/vmx/images/vFPC-20180605.img"

---
#External bridge configuration
BRIDGES:
    - type  : external
      name  : br-ext                  # Max 10 characters

--- 
#vRE VM parameters
CONTROL_PLANE:
    vcpus       : 1
    memory-mb   : 1024 
    console_port: 8601

    interfaces  :
      - type      : static
        ipaddr    : 10.102.144.94 
        macaddr   : "0A:00:DD:C0:DE:0E"

--- 
#vPFE VM parameters
FORWARDING_PLANE:
    memory-mb   : 8192 
    vcpus       : 4
    console_port: 8602
    device-type : virtio 

    interfaces  :
      - type      : static
        ipaddr    : 10.102.144.98
        macaddr   : "0A:00:DD:C0:DE:10"

--- 
#Interfaces
JUNOS_DEVICES:
   - interface            : ge-0/0/0
     mac-address          : "02:06:0A:0E:FF:F0"
     description          : "ge-0/0/0 interface"
   
   - interface            : ge-0/0/1
     mac-address          : "02:06:0A:0E:FF:F1"
     description          : "ge-0/0/0 interface"
   
   - interface            : ge-0/0/2
     mac-address          : "02:06:0A:0E:FF:F2"
     description          : "ge-0/0/0 interface"
   
   - interface            : ge-0/0/3
     mac-address          : "02:06:0A:0E:FF:F3"
     description          : "ge-0/0/0 interface"

```

Install

```
root@ubuntu18:/root/vmx# sudo ./vmx.sh -lv --install
```

```
root@ubuntu18:/root/vmx# cat config/vmx-junosdev.conf
##############################################################
#
#  vmx-junos-dev.conf
#  - Config file for junos device bindings.
#  - Uses YAML syntax. 
#  - Leave a space after ":" to specify the parameter value.
#  - For physical NIC, set the 'type' as 'host_dev'
#  - For junos devices, set the 'type' as 'junos_dev' and
#    set the mandatory parameter 'vm-name' to the name of
#    the vPFE where the device exists
#  - For bridge devices, set the 'type' as 'bridge_dev'
#
##############################################################
interfaces :

     - link_name  : vmx_link1
       mtu        : 1500
       endpoint_1 : 
         - type        : junos_dev
           vm_name     : vmx1 
           dev_name    : ge-0/0/0
       endpoint_2 :
         - type        : host_dev
           dev_name    : ens19

     - link_name  : vmx_link2
       mtu        : 1500
       endpoint_1 : 
         - type        : junos_dev
           vm_name     : vmx1
           dev_name    : ge-0/0/1
       endpoint_2 :
         - type        : host_dev
           dev_name    : ens20

     - link_name  : vmx_link3
       endpoint_1 : 
         - type        : junos_dev
           vm_name     : vmx1
           dev_name    : ge-0/0/2
       endpoint_2 :
         - type        : host_dev
           dev_name    : ens21

     - link_name  : vmx_link4
       endpoint_1 : 
         - type        : junos_dev
           vm_name     : vmx1
           dev_name    : ge-0/0/3
       endpoint_2 :
         - type        : host_dev
           dev_name    : ens22

```

Bind interfaces

```
root@ubuntu18:/root/vmx# sudo ./vmx.sh --bind-dev
```



Console to vMX

```
root@ubuntu18:/root/vmx# ./vmx.sh --console vcp vmx1
```




On Juniper to enable ge-* interfaces

```
cli
configure
set chassis fpc 0 lite-mode
request system reboot
```

```
cli
configure
delete chassis auto-image-upgrade
```


Issue with terminal output
```
Message from syslogd@VMX1 at Nov 4 ...
VMX1 fpc0 Fram 0: sp = 0xff... , pc = 0xf7...
```
Tried the following

```
set system syslog user * pfe none
```




--------------------------

### Useful commands

Help

```
root@ubuntu18:/root/vmx# ./vmx.sh --help

Usage: vmx.sh [CONTROL OPTIONS]
       vmx.sh [LOGGING OPTIONS] [CONTROL OPTIONS]
       vmx.sh [JUNOS-DEV BIND OPTIONS]
       vmx.sh [CONSOLE LOGIN OPTIONS]

    CONTROL OPTIONS:
       --install                      : Install And Start vMX
       --start                        : Start vMX
       --stop                         : Stop vMX
       --restart                      : Restart vMX
       --status                       : Check Status Of vMX
       --cleanup                      : Stop vMX And Cleanup Build Files
       --cfg <file>                   : Override With The Specified vmx.conf File
       --env <file>                   : Override With The Specified Environment .env File
       --build <directory>            : Override With The Specified Directory for Temporary Files
       --help                         : This Menu

    LOGGING OPTIONS:
       -l                             : Enable Logging
       -lv                            : Enable Verbose Logging
       -lvf                           : Enable Foreground Verbose Logging

    JUNOS-DEV BIND OPTIONS:
       --bind-dev                     : Bind Junos Devices
       --unbind-dev                   : Unbind Junos Devices
       --bind-check                   : Check Junos Device Bindings
       --cfg <file>                   : Override With The Specified vmx-junosdev.conf File

    CONSOLE LOGIN OPTIONS:
       --console [vcp|vfp] [vmx_id]   : Login to the Console of VCP/VFP

    VFP Image OPTIONS:
       --vfp-info <VFP Image Path>    : Display Information About The Specified vFP image

Copyright(c) Juniper Networks, 2015
```



Console

```
./vmx.sh --console vcp vmx1
```

Stop

```
./vmx.sh -lv --stop
```

Start

```
./vmx.sh -lv --start
```

Bind check

```
./vmx.sh --bind-check
```
Bind

```
./vmx.sh --bind-dev
```
