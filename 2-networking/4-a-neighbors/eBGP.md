# eBGP

The default TTL for eBGP is 1 hop.

Also the eBGP default is to only allow **Directly Conncted**.

If your eBGP session is going to be more than 1 hop you can use:

```
neighbor <x.x.x.x> ebgp-multihop <ttl-value>
```

```ttl-value``` can be between 2-255

This will allow multihop and disabled the directly connected check.

What if your neighbor/peer is only 1 hop away but is not directly connected?  You may be peering between loopbacks.

In this case **do not** use ```ebgp-multihop```, rather use ```disable-connected-check```

```
neighbor <x.x.x.x> disable-connected-check
```

One way to check if an destination is directly connected is through hidden command.

```
show ip cef <x.x.x.x> samecable
```
