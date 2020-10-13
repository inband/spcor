# Test 1 - getting a handle on Multicast

No expierience with Multicast

Started out watching INE Multicast - bad idea. I'll watch this after I've read(Cisco Press/rfc's and labbed).  

Found Beau Williamson's "Fundamentals of IP Multicast" Live Lesson.  This is a much more gentler introduction.  

So far my initial understanding of multicast:

* Multicast is used for 'targeted' broadcasts that traverse routers (broadcast domain)
* or a special sub-set for link-local that basically operate like broadcast ```224.0.0.0/24``` ie OSPF, HSRP, IGMPv3
* The use case I like to think of is bradcast television - IPTV
* like unicast there is a SRC IP
* unlike unicast there is NOT a DST IP rather a "receiver"
* the "receiver" can be part of a GROUP
* the "receiver" IP is in multicast allocation of IPs
* the "receiver" tunes into the "src"
* the "receiver" knows about the "src" and this is used to build/signal a source tree from "receiver" to "src"(sender)
* setup is **BACKWARDS**
* the last hop router (the router directly connected to the "receiver") accepts an **IGMP Join** from "receiver"
* the **IGMP Join** is a host to router message
* the last hop router looks up the "SRC" in RIB(this will be used for RPF verification) and builds a MROUTE table.
* then last hop router sends **PIM Join** out toward "src"
* **PIM Join** is router to router message
* next router looks up "SRC ip** in RIB and build MROUTE table
* and so on
* until we get to FHR - first hop router (the router directly connected to the source)
* once FHR is setup the path is in effect signalled and "multicast" flow can occur.


Anyway, I need a break from theory so time to play around and get a proper feel for it.

I'm going to setup IGMPv3.

Using AS64511 (```csr5```, ```csr6```, ```csr7```, ```csr8```)

I have unicast routing in the GLOBAL routing table.

I'll make ```csr5``` the source and ```csr8``` the receiver.

```csr5``` to ```csr8``` is actually ECMP over OSPFv2 so that might be interesting - if that complicates things then I'll disable it.






























