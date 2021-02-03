# EPC buffer

These are the defaults

```
csr8#show monitor capture TEST | sec Buffer
  Buffer Details: 
   Buffer Type: LINEAR (default)
   Buffer Size (in MB): 10
```

Options

```
csr8#monitor capture TEST buffer ?
  circular  circular buffer
  size      Size of buffer in MB
```

Circular should be continuous - does it overwrite existing buffer?


Size

```
csr8#monitor capture TEST buffer circular size ?
  <1-100>  Total size of file(s) in MB
```

Show statistics

```
csr8#show monitor capture TEST buffer 
 buffer size (KB) : 10240
 buffer used (KB) : 0
 packets in buf   : 0
 packets dropped  : 0
 packets per sec  : 0

csr8#monitor capture TEST start            
Started capture point : TEST
csr8#
*Feb  3 00:58:13.901: %BUFCAP-6-ENABLE: Capture Point TEST enabled.
csr8#
csr8#
csr8#show monitor capture TEST buffer 
 buffer size (KB) : 10240
 buffer used (KB) : 128
 packets in buf   : 5
 packets dropped  : 0
 packets per sec  : 1
```

------------

Where is it stored?


