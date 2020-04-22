---
layout: post
title: 'Arch Linux: qemu'
date: '2015-12-13 14:46:16'
---

Create block device image:

```
$ qemu-img create -f raw linux-distro.img 10G
```

Boot a linux distro from it's live cd iso on qemu in legacy BIOS MBR mode:

```
$ qemu-system-x86_64 -enable-kvm -m 2G -boot order=d -cdrom linux-distro.iso -drive format=raw,file=linux-distro.img
```

Or specify UEFI firmware:

```
$ qemu-system-x86_64 -enable-kvm -m 2G -bios /usr/share/ovmf/ovmf_x64.bin -cdrom linux-distro.iso -drive format=raw,file=linux-distro.img
```

Boot after installation:

```
$ qemu-system-x86_64 -enable-kvm -m 2G -drive format=raw,file=linux-distro.img
```