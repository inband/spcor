# mpls routing table integration

By default, Juniper devices only forward via MPLS inet.3 to destinations learned via BGP.


IGP

ISIS/15 OSPF/10 -> inet.0


MP-BGP VPN

LDP/9 RSVP/7 -> inet.3

-----------------------

BGP IP resolution uses both **inet.0** and **inet.3**






