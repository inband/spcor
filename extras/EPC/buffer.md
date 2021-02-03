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

Where is it stored?
