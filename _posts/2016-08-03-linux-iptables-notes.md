---
layout: post
title: 'Linux: iptables notes'
date: '2016-08-03 13:11:32'
---

My starting rules for iptables.

```
# Generated by iptables-save v1.6.0 on Tue Aug  2 15:27:18 2016
*mangle
:PREROUTING ACCEPT [2088:306423]
:INPUT ACCEPT [2088:306423]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [1154:235381]
:POSTROUTING ACCEPT [1155:235413]
COMMIT
# Completed on Tue Aug  2 15:27:18 2016
# Generated by iptables-save v1.6.0 on Tue Aug  2 15:27:18 2016
*nat
:PREROUTING ACCEPT [1863:150056]
:INPUT ACCEPT [53:6411]
:OUTPUT ACCEPT [4:225]
:POSTROUTING ACCEPT [1004:40225]
COMMIT
# Completed on Tue Aug  2 15:27:18 2016
# Generated by iptables-save v1.6.0 on Tue Aug  2 15:27:18 2016
*filter
:INPUT DROP [0:0]
:FORWARD DROP [0:0]
:OUTPUT ACCEPT [0:0]
:TCP - [0:0]
:UDP - [0:0]
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -m conntrack --ctstate INVALID -j DROP
-A INPUT -p icmp -m icmp --icmp-type 8 -m conntrack --ctstate NEW -j ACCEPT
-A INPUT -p udp -m conntrack --ctstate NEW -j UDP
-A INPUT -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -m conntrack --ctstate NEW -j TCP
-A TCP -p tcp -m tcp --dport 80 -j ACCEPT
-A TCP -p tcp -m tcp --dport 443 -j ACCEPT
-A TCP -p tcp -m tcp --dport 22 -j ACCEPT
COMMIT
# Completed on Tue Aug  2 15:27:18 2016

```