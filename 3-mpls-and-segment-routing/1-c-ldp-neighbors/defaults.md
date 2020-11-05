# Defaults

IOS

```
mpls ldp discovery hello interval 5
mpls ldp discovery hello holdtime 15
```



IOSXR

```
RP/0/RP0/CPU0:XR1#show mpls ldp parameters 
Thu Nov  5 00:37:08.701 UTC

LDP Parameters:
  Role: Active
  Protocol Version: 1
  Router ID: 11.11.11.11
  Null Label:
    IPv4: Implicit
  Session:
    Hold time: 180 sec
    Keepalive interval: 60 sec
    Backoff: Initial:15 sec, Maximum:120 sec
    Global MD5 password: Disabled
  Discovery:
    Link Hellos:     Holdtime:15 sec, Interval:5 sec
    Targeted Hellos: Holdtime:90 sec, Interval:10 sec
    Quick-start: Enabled (by default)
    Transport address:
      IPv4: 11.11.11.11
  Graceful Restart:
    Disabled
  NSR: Disabled, Not Sync-ed
  Timeouts:
    Housekeeping periodic timer: 10 sec
    Local binding: 300 sec
    Forwarding state in LSD: 15 sec
  Delay in AF Binding Withdrawl from peer: 180 sec
  Max:
    4300 interfaces (4000 attached, 300 TE tunnel), 2000 peers
  OOR state
    Memory: Normal
```
