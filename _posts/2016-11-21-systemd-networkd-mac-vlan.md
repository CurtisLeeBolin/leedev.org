---
layout: post
title: 'systemd-networkd: mac vlan'
date: '2016-11-21 04:37:21'
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
IPForward=yes
LinkLocalAddressing=no
```

```
#/etc/systemd/network/en.network

[Match]
Name=en*

[Network]
MACVLAN=mv0
LinkLocalAddressing=no
```

NOTE: The `LinkLocalAddressing=no` is not needed.  I add it because I have no use for local link addresses.