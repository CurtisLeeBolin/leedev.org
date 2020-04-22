---
layout: post
title: 'Arch Linux: Disable makepkg compression'
date: '2015-07-30 07:36:26'
---

I have a friend that complained about the incredible amount of time makepkg takes to compress the google-chrome AUR package when installing it on a netbook with a low performance CPU.

Since he and I are not really concerned about the size of the package, I decided to take a quick look to see if I could disable the compression.  It only took a moment of looking at the makepkg manual.

```
# nano /etc/makepkg.conf
```

```
#PKGEXT='.pkg.tar.xz'
PKGEXT='.pkg.tar'
#SRCEXT='.src.tar.gz'
SRCEXT='.src.tar'
```
Now when installing google-chrome from the AUR, the archive is instantly created and installed.  I'm keeping the change even on my computers with much higher performance CPUs.