---
layout: post
title: 'Arch Linux: create and mount a partitioned block device image'
date: '2015-12-13 14:54:54'
---

Create a block device image file:

 + 8GiB file named usb.img example:

```
$ dd if=/dev/zero of=usb.img bs=1M count=$((8*1024))
8192+0 records in
8192+0 records out
8589934592 bytes (8.6 GB, 8.0 GiB) copied, 15.1569 s, 567 MB/s
```

Partition and make filesystem:

```
# losetup -f --show usb.img
/dev/loop0
# fdisk /dev/loop0
# partprobe /dev/loop0
# mkfs.vfat -F32 /dev/loop0p1
# losetup -d /dev/loop0
```
View partition layout of the block device image:

```
$ fdisk -l usb.img
Disk usb.img: 8 GiB, 8589934592 bytes, 16777216 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd37991cb

Device     Boot Start      End  Sectors Size Id Type
usb.img1         2048 16777215 16775168   8G  c W95 FAT32 (LBA)

```

The offset of the partition is calculated by multipying the `Start` of the partition by the `Units`.

Start * Units = offset
2048 * 512 = 1048576

Loop mount the partition:

```
# mount -o loop,offset=$((2048*512)) usb.img /mnt/usb
```