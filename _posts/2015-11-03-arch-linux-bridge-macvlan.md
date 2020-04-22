---
layout: post
title: 'systemd-networkd: bridge + macvlan'
date: '2015-11-03 15:53:32'
---

Document List:

* [systemd-networkd](http://www.freedesktop.org/software/systemd/man/systemd-networkd.service.html)
* [systemd.network]()
* [systemd.netdev]()

[Random MAC Generator](http://www.hellion.org.uk/cgi-bin/randmac.pl?scope=global&oui=00%3A16%3A3e&type=unicast)

```
#/etc/systemd/network/mv0.netdev

[NetDev]
Name=mv0
Kind=macvlan
MACAddress=00:16:3e:4b:96:25

[MACVLAN]
Mode=bridge
```

```
#/etc/systemd/network/mv0.network

[Match]
Name=mv0

[Network]
DHCP=yes
LinkLocalAddressing=no
```

```
#/etc/systemd/network/br0.netdev

[NetDev]
Name=br0
Kind=bridge
```

```
#/etc/systemd/network/br0.network

[Match]
Name=br0

[Network]
MACVLAN=mv0
LinkLocalAddressing=no
```

```
#/etc/systemd/network/en.network

[Match]
Name=en*

[Network]
Bridge=br0
LinkLocalAddressing=no
```

NOTE: The `LinkLocalAddressing=no` is not needed.  I add it because I have no use for local link addresses.