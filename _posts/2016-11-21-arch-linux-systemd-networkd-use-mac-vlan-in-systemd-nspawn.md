---
layout: post
title: 'systemd-networkd: use mac vlan in systemd-nspawn'
date: '2016-11-21 04:44:02'
---

Document List:

* [systemd-networkd](http://www.freedesktop.org/software/systemd/man/systemd-networkd.service.html)
* [systemd.network]()
* [systemd.netdev]()

[Random MAC Generator](http://www.hellion.org.uk/cgi-bin/randmac.pl?scope=global&oui=00%3A16%3A3e&type=unicast)

```
#/etc/systemd/network/mv-mv0.network

[Match]
Name=mv-mv0

[Link]
MACAddress=00:16:3e:03:94:24

[Network]
DHCP=yes
```