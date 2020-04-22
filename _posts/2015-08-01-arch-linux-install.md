---
layout: post
title: 'Arch Linux: Legacy BIOS Install Notes'
date: '2015-08-01 09:02:47'
---

Boot the Arch Linux ISO using Legacy BIOS.

Change shell to bash and reload shell:

```
# usermod -s /bin/bash root
# exit
```

Partition your storage device with Master Boot Record `MBR` partition table:

```
# fdisk /dev/sda
```

This is the how my SSD is partitioned:

```
NAME    SIZE    MOUNTPOINT
sda     500.0G
├─sda1  492.0G  part /,/home/
└─sda2    8.0G  part [SWAP]
```

If you created a swap partition, make swap space and enable it:

```
# mkswap /dev/sda2
# swapon /dev/sda2
```

Make the BTRFS on the partition for Arch Linux:

```
# mkfs.btrfs /dev/sda1
```

Create BTRFS subvolumes:

```
# mount /dev/sda1 /mnt
# btrfs subvolume create /mnt/@arch
# btrfs subvolume create /mnt/@home
# umount /mnt
```

Mount the subvolumes:

```
# mount -o subvol=@arch /dev/sda1 /mnt
# mkdir /mnt/home
# mount -o subvol=@home /dev/sda1 /mnt/home
```

Create the base bootstrap Arch Linux root file system:

```
# pacstrap /mnt base
```

Create the file system table and save it:

```
# genfstab -p -U /mnt > /mnt/etc/fstab
```

Change Root `chroot` into your newly bootstrapped Arch Linux:

```
# arch-chroot /mnt /bin/bash
```

Set the hostname:

```
# echo "leedev" > /etc/hostname
```

Set the system timezone:

```
# rm /etc/localtime
# ln -s /usr/share/zoneinfo/US/Central /etc/localtime
```

Configure and generate locales:

```
# echo "LANG=en_US.UTF-8" > /etc/locale.conf
# echo "en_US.UTF-8 UTF-8" > /etc/locale.gen
# export LANG=en_US.UTF-8
# locale-gen
```

Configure the keyboard keymap console:

```
# echo "KEYMAP=us" > /etc/vconsole.conf
```

Create a user:

```
# useradd --home-dir /home/leedev --create-home --gid users --groups wheel,sys --shell /bin/bash leedev
```

Set the password for this new user:

```
# passwd leedev
```

Install a lean but complete Gnome desktop environment:

```
# pacman -S tmux sudo openssh rsync bash-completion git grub os-prober btrfs-progs ecryptfs-utils sysstat gdm gnome-shell gnome-terminal nautilus gnome-control-center gnome-tweak-tool ttf-liberation system-config-printer gnome-backgrounds gnome-keyring gnome-disk-utility baobab gnome-screenshot cheese evince eog totem mpv geany file-roller networkmanager gst-libav libmtp ttf-freefont libva-vdpau-driver libdvdcss pkgfile xorg-server xorg-xinit libvdpau xf86-video-amdgpu xf86-input-libinput mesa-vdpau mesa-libgl gst-plugins-ugly xf86-input-libinput gimp chromium cups ghostscript gsfonts foomatic-db foomatic-db-engine foomatic-db-nonfree seahorse
```

Give the `wheel` group `sudo` permissions:

```
# echo "%wheel ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/group_wheel
```

Edit `/etc/shadow` to prevent root login:

```
# nano /etc/shadow
------------------
root:!:14871::::::
```

Create the `initramfs`:

```
# mkinitcpio -p linux
```

Make and save the grub configuration:

```
# grub-mkconfig -o /boot/grub/grub.cfg
```

Install Grub to the MBR:

```
# grub-install /dev/sda
```

Configure the __gdm__, __sshd__, __NetworkManager__, __systemd-timesyncd__, and __cups__ services to start at boot:

```
# systemctl enable gdm.service sshd.service NetworkManager.service systemd-timesyncd.service org.cups.cupsd.service
```

Exit the `chroot`:

```
# exit
```

Unmount the newly created file systems:

```
# umount -R /mnt
```

Reboot and enjoy Arch Linux:

```
# reboot
```