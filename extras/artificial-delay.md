# cisco delay


Create a delay - in lab everything is sub 1 millisecond - so behaviour and testing differs from PRODUCTION testing.

```
lab-mel-r1(config-if)#delay ?
  <1-16777215>  Throughput delay (tens of microseconds)
```

This didn't work as expected - I tried on interface and sub-interface.

I'll have to research further and come back.

Looks like it is used cost best route metrics for routing protocols (ie EIGRP)
