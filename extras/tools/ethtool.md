# ethtool

Cant ssh from Debian **host1** to CSRv **CUSTX-CPE1** same subnet.

Incorrect chksum

```
root@host1:~# tcpdump -nni eth0 -v
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes

12:58:07.295167 ARP, Ethernet (len 6), IPv4 (len 4), Request who-has 1.1.1.1 tell 1.1.1.2, length 28
12:58:07.296050 ARP, Ethernet (len 6), IPv4 (len 4), Reply 1.1.1.1 is-at 02:1f:64:69:fa:f2, length 46

12:58:07.296060 IP (tos 0x0, ttl 64, id 811, offset 0, flags [DF], proto TCP (6), length 60)
    1.1.1.2.60084 > 1.1.1.1.22: Flags [S], cksum 0x0433 (incorrect -> 0xf54f), seq 25292868, win 64240, options [mss 1460,sackOK,TS val 2270162906 ecr 0,nop,wscale 7], length 0
12:58:08.298720 IP (tos 0x0, ttl 64, id 812, offset 0, flags [DF], proto TCP (6), length 60)
    1.1.1.2.60084 > 1.1.1.1.22: Flags [S], cksum 0x0433 (incorrect -> 0xf164), seq 25292868, win 64240, options [mss 1460,sackOK,TS val 2270163909 ecr 0,nop,wscale 7], length 0

```


Install ethtool and turn off chksum

```
root@host1:~# apt install ethtool




root@host1:~# ethtool -k eth0
Features for eth0:
rx-checksumming: on
tx-checksumming: on
	tx-checksum-ipv4: off [fixed]
	tx-checksum-ip-generic: on
	tx-checksum-ipv6: off [fixed]
	tx-checksum-fcoe-crc: off [fixed]
	tx-checksum-sctp: on
scatter-gather: on
	tx-scatter-gather: on
	tx-scatter-gather-fraglist: on
tcp-segmentation-offload: on
	tx-tcp-segmentation: on
	tx-tcp-ecn-segmentation: on
	tx-tcp-mangleid-segmentation: on
	tx-tcp6-segmentation: on
udp-fragmentation-offload: off
generic-segmentation-offload: on
generic-receive-offload: on
large-receive-offload: off [fixed]
rx-vlan-offload: on
tx-vlan-offload: on
ntuple-filters: off [fixed]
receive-hashing: off [fixed]
highdma: on
rx-vlan-filter: off [fixed]
vlan-challenged: off [fixed]
tx-lockless: on [fixed]
netns-local: off [fixed]
tx-gso-robust: off [fixed]
tx-fcoe-segmentation: off [fixed]
tx-gre-segmentation: on
tx-gre-csum-segmentation: on
tx-ipxip4-segmentation: on
tx-ipxip6-segmentation: on
tx-udp_tnl-segmentation: on
tx-udp_tnl-csum-segmentation: on
tx-gso-partial: off [fixed]
tx-sctp-segmentation: on
tx-esp-segmentation: off [fixed]
tx-udp-segmentation: off [fixed]
fcoe-mtu: off [fixed]
tx-nocache-copy: off
loopback: off [fixed]
rx-fcs: off [fixed]
rx-all: off [fixed]
tx-vlan-stag-hw-insert: on
rx-vlan-stag-hw-parse: on
rx-vlan-stag-filter: off [fixed]
l2-fwd-offload: off [fixed]
hw-tc-offload: off [fixed]
esp-hw-offload: off [fixed]
esp-tx-csum-hw-offload: off [fixed]
rx-udp_tunnel-port-offload: off [fixed]
tls-hw-tx-offload: off [fixed]
tls-hw-rx-offload: off [fixed]
rx-gro-hw: off [fixed]
tls-hw-record: off [fixed]




root@host1:~# ethtool -K eth0 tx off rx off
Actual changes:
rx-checksumming: off
tx-checksumming: off
	tx-checksum-ip-generic: off
	tx-checksum-sctp: off
tcp-segmentation-offload: off
	tx-tcp-segmentation: off [requested on]
	tx-tcp-ecn-segmentation: off [requested on]
	tx-tcp-mangleid-segmentation: off [requested on]
	tx-tcp6-segmentation: off [requested on]
```
