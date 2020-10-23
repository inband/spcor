# ssh

```
RP/0/RP0/CPU0:xrv-template#crypto key generate rsa 
Fri Oct 23 09:11:41.521 UTC
The name for the keys will be: the_default
  Choose the size of the key modulus in the range of 512 to 4096 for your General Purpose Keypair. Choosing a key modulus greater than 512 may take a few minutes.

How many bits in the modulus [2048]: 
Generating RSA keys ...
Done w/ crypto generate keypair
[OK]

```


```
RP/0/RP0/CPU0:xrv-template#show run ssh
Fri Oct 23 09:18:35.849 UTC
ssh server vrf LAB_MGMT

```


```
RP/0/RP0/CPU0:xrv-template#show ssh 
Fri Oct 23 09:17:14.399 UTC
SSH version : Cisco-2.0 

id       chan pty     location        state           userid    host                  ver authentication connection type
-------------------------------------------------------------------------------------------------------------------------------
Incoming sessions
2        1    vty0    0/RP0/CPU0      SESSION_OPEN    admin     192.168.18.1          v2  key-intr       Command-Line-Interface 

Outgoing sessions
```
