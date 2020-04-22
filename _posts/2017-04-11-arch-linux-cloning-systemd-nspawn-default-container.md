---
layout: post
title: 'Arch Linux: Cloning systemd-nspawn Default Container'
date: '2017-04-11 15:34:36'
---

Create a `btrfs` snapshot of the default container:

```
# btrfs subvolume snapshot /var/lib/machines/default /var/lib/machines/clone
```

Change the mac address:
[Random Mac Address](https://www.hellion.org.uk/cgi-bin/randmac.pl?scope=global&oui=00%3A16%3A3e&type=unicast)

```
# nano /var/lib/machines/clone/etc/systemd/network/mv-mvl0.network
-------------------------------------------
[Match]
Name=mv-mvl0

[Link]
MACAddress=00:16:3e:8c:7d:f8

[Network]
DHCP=yes
LinkLocalAddressing=no
```

Start and Enable the container:

```
$ sudo machinectl start clone
$ sudo machinectl enable clone
```