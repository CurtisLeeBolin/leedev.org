---
layout: post
title: 'Gnome: Open Archives in File Roller by Default'
date: '2026-09-23 10:05:18'
---

By default opening an archive file in Nautilus uncompresses the archive file instead of opening it in File Roller.  Uncompressing options are in the right click menu. I prefer to not right click on the file, click `Open With...`, then search for File Roller just to view the files in the archive file.

```bash
mimes=(
  application/zip
  application/x-tar
  application/x-7z-compressed
  application/x-rar-compressed
  application/x-gtar
  application/x-gzip
  application/x-bzip2
  application/x-lzma
  application/x-xz
  application/x-zstd
  application/gzip
  application/x-bzip
  application/x-bzip2
  application/x-lzma
  application/x-xz
  application/zstd
  application/x-compress
  application/vnd.debian.binary-package
  application/x-rpm
  application/x-sharedlib
  application/x-cpio
  application/x-shar
  application/x-lha
  application/x-arj
  application/x-ace-compressed
)

for mime in "${mimes[@]}"; do
  xdg-mime default org.gnome.FileRoller.desktop "${mime}"
done
```

