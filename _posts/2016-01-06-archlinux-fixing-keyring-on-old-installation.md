---
layout: post
title: 'Arch Linux: fixing keyring on old installation'
date: '2016-01-06 15:45:34'
---

If you have an old installation of Arch Linux that hasn't been updated in quite a while, you will probably get a keyring error.  This is how to correct that:

```
# pacman-key --init
# pacman-key --populate archlinux
# pacman-key --refresh-keys
```