---
layout: post
title: 'Arch Linux: Dual Boot Fix for Bad UEFI Firmware'
date: '2015-09-05 01:18:59'
---

Some firmware likes to rewrite the UEFI boot order every time MS Windows' UEFI loader is found on the UEFI partition.  This is the workaround.

Rename the `Microsoft` directory in your UEFI partition to `Windows_10`.

Then create a static grub entry in `/etc/grub.d/40_custom`:
NOTE: change the UUID on the `search` lines to the UUID of your UEFI partition.  Mine was `6EB4-4B14`. Use `blkid` to find the partition UUID.
```
menuentry 'Microsoft Windows 10 Enterprise' --class windows --class os $menuentry_id_option 'osprober-efi-6EB4-4B14' {
  insmod part_gpt
  insmod fat
  set root='hd0,gpt1'
  if [ x$feature_platform_search_hint = xy ]; then
    search --no-floppy --fs-uuid --set=root --hint-bios=hd0,gpt1 --hint-efi=hd0,gpt1 --hint-baremetal=ahci0,gpt1  6EB4-4B14
  else
    search --no-floppy --fs-uuid --set=root 6EB4-4B14
  fi
  chainloader /EFI/Windows_10/Boot/bootmgfw.efi
}
```

Update `grub.conf`:
```
# grub-mkconfig -o /boot/grub/grub.cfg
```
Then reinstall grub:
```
# grub-install
```