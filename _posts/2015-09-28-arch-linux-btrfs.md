---
layout: post
title: 'Arch Linux: BTRFS notes'
date: '2015-09-28 16:51:12'
---

+ In __fstab__ make sure __pass__, the last column of a line, is set to __0__ since btrfs does __not__ do a file system check at mount.
+ To copy files using Copy-On-Write add the `--reflink` option for `cp`, <br />f.e.: `$ cp --reflink=always <source> <dest>`

+ HDD
 + Recomended mount options: `rw,noatime,autodefrag,compress-force=lzo,space_cache`

+ SSD
 + Recommended mount options: `rw,noatime,ssd,compress=lzo,space_cache`
 + Until SATA version 3.1, TRIM was not asynchronous, so it is better to not use the __discard__ mount option.  __systemd__ has a solution for this, __fstrim.timer__.  It will discard unused blocks once a week. `systemctl enable fstrim.timer` to enable it. To check the version of SATA in use for your device, run `# smartctl --info <device>`. Also, fstrim is suggested to only be ran once per week.