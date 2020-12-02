# tpid

THe tpid can be specified.  

```
root@VMX2# set interfaces ge-0/0/2 unit 223 vlan-tags outer 0x8100.223 inner 11    

[edit]
root@VMX2# commit 
commit complete

[edit]
root@VMX2# show interfaces ge-0/0/2.223 
vlan-tags outer 0x8100.223 inner 11;
family inet {
    address 10.10.11.1/24;
}
```


0x88a8 was not allowed

```
root@VMX2# set interfaces ge-0/0/2 unit 223 vlan-tags outer 0x88A8.223 inner 11 

[edit]
root@VMX2# commit 
[edit interfaces ge-0/0/2 unit 223]
  'vlan-tags'
    Undefined tag-protocol-id 0x88a8
error: configuration check-out failed
```
