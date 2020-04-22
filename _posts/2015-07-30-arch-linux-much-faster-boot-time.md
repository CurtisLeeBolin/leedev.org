---
layout: post
title: 'Arch Linux: Much Faster Boot Time'
date: '2015-07-30 18:00:18'
---

I decided to read the mkinitcpio manual to boost the already fast boot time on my notebook computer.  I learned that the `systemd` hook could be used instead of the default `base` and `udev` hooks which gives a little boost in boot time.  Another thing I noticed at boot was the decompression of the `initfs` seemed a little slow at boot.  I decided to research which of the supported compression methods is the fastest at decompression. I found that `lz4` at its highest compression level, `-9`, has the fasted decompression.  It is actually _MANY_ times faster then any other supported compression.

**First edit `mkinitcpio.conf`:**

```
# nano /etc/mkinitcpio.conf
```

```
# HOOKS
#HOOKS="base udev autodetect modconf block filesystems keyboard fsck"
HOOKS="systemd autodetect modconf block filesystems keyboard"

# COMPRESSION
COMPRESSION="lz4"

# COMPRESSION_OPTIONS
# Additional options for the compressor
#COMPRESSION_OPTIONS=""
COMPRESSION_OPTIONS="-9"
```

**Then rebuild your `initfs`:**

```
# mkinitcpio -p linux
```