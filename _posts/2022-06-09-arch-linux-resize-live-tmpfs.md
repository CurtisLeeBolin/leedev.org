---
layout: post
title: 'Arch Linux: resize live tmpfs'
date: '2022-06-09 00:00:00'
---

# Resize Arch Linux Live `tmpfs`

I needed to install some packages on the live session, but `pacman` complained that there wasn't enough space.

```
# mount -o remount,size=4G /run/archiso/cowspace
```
