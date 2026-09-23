---
layout: post
title: 'Gnome: Open Archives in File Roller by Default'
date: '2026-09-23 10:05:18'
---

By default opening an archive file in Nautilus uncompresses the archive file instead of opening it in File Roller.  Uncompressing options are in the right click menu. I prefer to not right click on the file, click `Open With...`, then search for File Roller just to view the files in the archive file.

```bash
mimes=( application/zip application/x-tar application/gzip application/x-bzip2 application/x-xz application/x-7z-compressed application/vnd.rar application/x-rar )

for mime in "${mimes[@]}"; do
  xdg-mime default org.gnome.FileRoller.desktop "$mime"
done
```

