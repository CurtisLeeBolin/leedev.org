---
layout: post
title: 'Alpine Linux: Server Setup with Xen and libvirt'
date: '2016-03-17 04:07:26'
---

### NOTE: Work In Progress

Download the latest Alpine Linux with Xen ISO from: http://dl-3.alpinelinux.org/alpine/latest-stable/releases/x86_64/

e.g.: alpine-xen-3.3.1-x86_64.iso

```
# setup-alpine
```

resize fs and lvm

```
# apk add e2fsprogs-extras
# e2fsck -f /dev/vg0/lv_root
# resize2fs -p /dev/vg0/lv_root 10G
# lvreduce -L 10G /dev/vg0/lv_root
# e2fsck -f /dev/vg0/lv_root
# reboot
```

```
# apk add nano
```

```
# nano /etc/ssh/sshd_config
```

```
#PermitRootLogin prohibit-password
PermitRootLogin yes
```

```
# reboot
```

```
# apk add libvirt-qemu qemu-system-x86_64
# rc-update add libvirtd
# reboot
```