# demo license

[cisco-licensing](https://slexui.cloudapps.cisco.com/SWIFT/LicensingUI/Quickstart#)

Migrating Technology Package Licenses to Cisco IOS XE 3.13S

Starting with Cisco IOS XE 3.13S, the names of the technology package licenses changed as shown below.


The Standard technology package was changed to the IPBase technology package.


The Advanced technology package was changed to the Security technology package.


The Premium technology package was changed to the AX package.


```
iMac:~ nat-imac$ scp Downloads/9YIWRV5U3ZC_20200821013857458.lic admin@192.168.250.32:/9YIWRV5U3ZC_20200821013857458.lic
Password: 
Privilege denied.
```

```
hostname CSR2

aaa new-model
!
aaa authorization exec default local 
!
ip scp server enable
```


```
iMac:~ nat-imac$ scp Downloads/9YIWRV5U3ZC_20200821013857458.lic admin@192.168.250.32:/9YIWRV5U3ZC_20200821013857458.lic
Password: 
9YIWRV5U3ZC_20200821013857458.lic             100% 1239    34.6KB/s   00:00    
Connection to 192.168.250.32 closed by remote host.
```

```
CSR2#dir | inc lic
   14  -rw-             1239  Aug 21 2020 08:46:35 +00:00  9YIWRV5U3ZC_20200821013857458.lic
```


```
CSR2#license install bootflash:9YIWRV5U3ZC_20200821013857458.lic
CSR2#configure terminal  
CSR2(config)#license boot level premium
CSR2#wr mem
Building configuration...
[OK]
CSR2#reload
```
