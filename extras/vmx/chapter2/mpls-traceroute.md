# mpls traceroute

```
root@VMX1:PE1> traceroute mpls ldp 172.16.0.33 
  Probe options: ttl 64, retries 3, wait 10, paths 16, exp 7, fanout 16

  ttl    Label  Protocol    Address          Previous Hop     Probe Status
    1   299856  LDP         10.0.0.3         (null)           Success           
  FEC-Stack-Sent: LDP 
  ttl    Label  Protocol    Address          Previous Hop     Probe Status
    2        3  LDP         10.0.0.9         10.0.0.3         Egress            
  FEC-Stack-Sent: LDP 

  Path 1 via lt-0/0/0.12 destination 127.0.0.64
```


Not sure how this behaves.  Tried to involve XRv

```
root@VMX1:PE1> traceroute mpls ldp 172.16.0.44    
  Probe options: ttl 64, retries 3, wait 10, paths 16, exp 7, fanout 16

  ttl    Label  Protocol    Address          Previous Hop     Probe Status
    1   299824  LDP         10.0.0.3         (null)           Unhelpful         
  FEC-Stack-Sent: LDP 
  ttl    Label  Protocol    Address          Previous Hop     Probe Status
    2                       (null)           10.0.0.3         No reply          
    3                       (null)           (null)           No reply          
^C[abort]
```
