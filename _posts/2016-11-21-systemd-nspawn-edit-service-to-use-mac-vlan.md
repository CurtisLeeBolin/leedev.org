---
layout: post
title: 'systemd-nspawn: edit service to use mac vlan'
date: '2016-11-21 04:58:28'
---

```
# systemctl edit systemd-nspawn@.service
```

```
[Service]
ExecStart=
ExecStart=/usr/bin/systemd-nspawn --quiet --keep-unit --boot --link-journal=try-guest --network-macvlan=mv0 -U --settings=override --machine=%i
```