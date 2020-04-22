---
layout: post
title: 'SteamOS: debian installer cdrom bug'
date: '2015-12-15 09:40:22'
---

`No Common CD-ROM drive was detected.`

This is a very common problem for the Debian Installer when installing from a USB flash drive.

Thankfully I used Debian years ago and know how to circumvent this issue.

Steps in the Debian Installer:

+ Select `Expert install` at the grub menu
+ Select `Go Back` at the `Select a language` menu
+ Select `Execute a shell` at the `Debian installer main menu`
+ Select `Continue` at the `Execute a shell` warning screen
+ At the `Execute a shell` window:
+ + List partitions: `cat /proc/partitions`
+ + Determine the partition of your flash drive that contains the installer. (NOTE: It will be smaller than your hard drive) Mine was `/dev/sdb1`
+ + Make the cdrom directory at root: `mkdir /cdrom`
+ + Mount the partition to `/cdrom`: `mount -t vfat /dev/sdb1 /cdrom`
+ + Exit the shell: `exit`
+ Continue with the Debian installer to install SteamOS.