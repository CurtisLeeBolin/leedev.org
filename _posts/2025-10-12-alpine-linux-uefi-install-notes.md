---
layout: post
title: 'Alpine Linux: UEFI Install Notes'
date: '2025-10-12 15:30:49'
---


# Local Linux Computer

USB flash drive
  - `gpt`
  - `ef00` partition
  - fat 32
  - mount


https://www.alpinelinux.org/downloads/

Downoad `EXTENDED` `86_64`

mount iso image
copy files to USB flash drive root


https://wiki.alpinelinux.org/wiki/Installation_on_a_headless_host

https://github.com/macmpi/alpine-linux-headless-bootstrap

download `headless.apkovl.tar.gz`

extract to USB flash drive root


UEFI boot USB flash drive on server

`$ ssh root@<IP>`


# Live Alpine Linux Session

```
# setup-apkcache
# setup-apkrepos
# apk update
# apk add lsblk wipefs gptfdisk btrfs-progs lvm2 nano
```

If you need to search for a package to install:

`# apk search <package or binary name>`

Partition your drive.

```
# wipefs --all /dev/<device name>
# gdisk /dev/<device name>
```

```
Command (? for help): n
Partition number (1-128, default 1):
First sector (34-976773134, default = 2048) or {+-}size{KMGTP}:
Last sector (2048-976773134, default = 976773119) or {+-}size{KMGTP}: +256M
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300): ef00
Changed type of partition to 'EFI system partition'

Command (? for help): n
Partition number (2-128, default 2):
First sector (34-976773134, default = 526336) or {+-}size{KMGTP}:
Last sector (526336-976773134, default = 976773119) or {+-}size{KMGTP}: +128G
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300):
Changed type of partition to 'Linux filesystem'

Command (? for help): n
Partition number (3-128, default 3):
First sector (34-976773134, default = 268961792) or {+-}size{KMGTP}:
Last sector (268961792-976773134, default = 976773119) or {+-}size{KMGTP}: -8G
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300): 8e00
Changed type of partition to 'Linux LVM'

Command (? for help): n
Partition number (4-128, default 4):
First sector (34-976773134, default = 959997952) or {+-}size{KMGTP}:
Last sector (959997952-976773134, default = 976773119) or {+-}size{KMGTP}:
Current type is 8300 (Linux filesystem)
Hex code or GUID (L to show codes, Enter = 8300): 8200
Changed type of partition to 'Linux swap'

Command (? for help): p
Disk /dev/sdc: 976773168 sectors, 465.8 GiB
Model: Samsung SSD 870
Sector size (logical/physical): 512/512 bytes
Disk identifier (GUID): 848B52CA-04ED-4770-B101-1361F048CEE9
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 976773134
Partitions will be aligned on 2048-sector boundaries
Total free space is 4062 sectors (2.0 MiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048          526335   256.0 MiB   EF00  EFI system partition
   2          526336       268961791   128.0 GiB   8300  Linux filesystem
   3       268961792       959995918   329.5 GiB   8E00  Linux LVM
   4       959997952       976773119   8.0 GiB     8200  Linux swap

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): y
OK; writing new GUID partition table (GPT) to /dev/sdc.
The operation has completed successfully.
```

```
# mkswap /dev/sdc4
# swapon /dev/sdc4
```

```
# pvcreate /dev/sdc3
# vgcreate vg0 /dev/sdc3
```

```
# mkfs.btrfs /dev/sdc2
# mount -t btrfs /dev/sdc2 /mnt/
```

```
# mkfs.vfat -F32 /dev/sdc1
# mkdir /mnt/boot/
# mount /dev/sdc1 /mnt/boot/
```

# Installation

https://wiki.alpinelinux.org/wiki/Alpine_setup_scripts

```
# export BOOTLOADER=grub
# export USE_EFI=true
# export DISKLABEL=gpt
```

```
# setup-alpine

Ip address for eth0? dhcp

###############################
#EXIT SETUP AT 'Disk & Install'
###############################
```

`# setup-disk -m sys /mnt/`

Remove USB flash drive

```
# umount /mnt/boot /mnt/
# reboot
```
