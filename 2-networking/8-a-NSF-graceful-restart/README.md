#  NSF / graceful restart




NSF

[Cisco documentation](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/xe-16/irg-xe-16-book/bgp-nonstop-forwarding-awareness.html)





Graceful restart

[RFC4724](https://tools.ietf.org/html/rfc4724)

[Cisco documentation](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/xe-16/irg-xe-16-book/bgp-graceful-restart-per-neighbor.html)

On Cisco ASR1000 graceful restart is disabled by default. It can be enabled globally, through a peer session template and/or per neighbor.  Neighbor takes priority over template which takes priority over global.

```
Neighbor capabilities:
    Route refresh: advertised and received(new)
    Address family IPv4 Unicast: advertised and received
    Graceful Restart Capability: advertised
```
