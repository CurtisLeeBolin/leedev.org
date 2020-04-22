---
layout: post
title: 'systemd: Ignore Notebook Lid Switch'
date: '2019-11-12 15:10:23'
---

Edit /etc/systemd/logind.conf :

    HandleLidSwitch=ignore

Apply Changes:

    # systemctl restart systemd-logind.service

Reference: [Arch Linux Wiki](https://wiki.archlinux.org/index.php/Power_management)

