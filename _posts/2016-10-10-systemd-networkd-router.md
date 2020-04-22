---
layout: post
title: 'systemd-networkd: simple router'
date: '2016-10-10 16:48:38'
---

Devices for this setup:

  * WAN: enp1s0
  * LAN: enp2s0

WAN device configuration.
```
[Match]
Name=enp1s0

[Network]
DHCP=yes
LinkLocalAddressing=no
```
NOTE: LinkLocalAddressing is not required.


LAN device configuration.
```
[Match]
Name=enp2s0

[Network]
LinkLocalAddressing=no
Address=172.16.0.1/27
DHCPServer=yes
IPForward=ipv4
IPMasquerade=yes

[DHCPServer]
DefaultLeaseTimeSec=60
DNS=8.8.8.8
```