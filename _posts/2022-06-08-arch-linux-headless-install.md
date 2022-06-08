---
layout: post
title: 'Arch Linux: Headless Install USB Flash'
date: '2022-06-08 08:00:00'
---

# Creating a USB flash drive for a headless install.

Install the needed tools:

```
# pacman -Syu dosfstools mtools libisoburn
```

Two text files are needed:

```
$ touch meta-data
$ nano user-data
```

```
#cloud-config
users:
  - name: root
    ssh_authorized_keys:
      - ssh-rsa ...

write_files:
- content: |
    [Security]
    PreSharedKey=...
  path: /var/lib/iwd/blah.psk
```

Create a FAT image with the label `CIDATA`:

```
$ mkfs.fat -C -n CIDATA cloud-init.img 2048
```

Copy the previously made files to the filesystem of the image file:

```
$ mcopy -i cloud-init.img meta-data user-data ::

```

Add this image to the installer ISO:

```
$ xorriso -indev archlinux-version-x86_64.iso -outdev archlinux-version-x86_64-with-cidata.iso -append_partition 3 0x0c cloud-init.img -boot_image any replay
```

Copy the image to the flash drive:

```
# pv archlinux-version-x86_64.iso > /dev/sdx
```
