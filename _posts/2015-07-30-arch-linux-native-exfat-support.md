---
layout: post
title: 'Arch Linux: Native exFAT support'
date: '2015-07-30 08:12:09'
---

For anyone that uses a camera with SDXC, you might have realized the default file system for SDXC is exFAT.  This is needed for recording video files over 4GB due to vFAT's 4GB file size limit.

The next thing you might have realized is the _SLOW_ speed of the exFAT FUSE module, but this is no longer a problem.  There is a native exFAT module for the Linux kernel.  It is not yet included in the mainline kernel, but can be easily installed in Arch Linux thanks to the AUR package `exfat-dkms-git`.  To have the module auto built and installed after Linux kernel updates, enable the systemd `dkms` service.

```
$ yaourt -Syua linux-headers exfat-dkms-git
```

```
# systemctl enable dkms.service
```