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
# btrfs subvolume create /mnt/@var
# umount /mnt
```

Mount the subvolumes:

```
# mount -o subvol=@arch /dev/sda1 /mnt
# mkdir /mnt/home
# mount -o subvol=@home /dev/sda1 /mnt/home
# mkdir /mnt/var
# mount -o subvol=@var /dev/sda1 /mnt/var
```

Create the base bootstrap Arch Linux root file system with linux-hardened instead of linux:

```
# pacstrap /mnt base linux linux-firmware nano
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

Edit `hosts`:

```
# nano /etc/hosts
------------------
127.0.0.1  localhost
::1        localhost
127.0.1.1  leedev.localdomain  leedev
```

Set the system timezone and generate `/etc/adjtime`:

```
# ln -s /usr/share/zoneinfo/US/Central /etc/localtime
# hwclock --systohc
```

Configure and generate locales:

```
# echo "en_US.UTF-8 UTF-8" > /etc/locale.gen
# locale-gen
# echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

Configure the keyboard keymap console:

```
# echo "KEYMAP=us" > /etc/vconsole.conf
```

Create a user:

```
# groupadd --gid 1000 leedev
# useradd --home-dir /home/leedev --create-home --uid 1000 --gid 1000 --groups wheel,sys --shell /bin/bash lee
```

Set the password for this new user:

```
# passwd leedev
```

Install a lean but complete Server or Gnome desktop environment:

Server:

```
# pacman -S tmux sudo openssh rsync bash-completion git grub btrfs-progs ecryptfs-utils
```

Notebook:

```
# pacman -S tmux sudo openssh rsync bash-completion git grub btrfs-progs ecryptfs-utils base-devel sysstat gdm gnome-shell gnome-terminal nautilus gnome-control-center gnome-tweak-tool ttf-liberation system-config-printer gnome-backgrounds gnome-keyring gnome-disk-utility baobab gnome-screenshot cheese evince epiphany eog totem mpv geany file-roller networkmanager dhclient gst-libav libmtp ttf-freefont noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra libva-vdpau-driver libdvdcss pkgfile xorg-server xorg-xinit libvdpau xf86-video-amdgpu xf86-input-libinput mesa-vdpau mesa-libgl gst-plugins-ugly xf86-input-libinput gimp inkscape cups ghostscript gsfonts foomatic-db foomatic-db-engine foomatic-db-nonfree seahorse os-prober
```

Give the `wheel` group `sudo` permissions:

```
# nano /etc/sudoers.d/group_wheel
---------------------------------
Defaults:%wheel runcwd=*
%wheel ALL=(ALL) NOPASSWD: ALL
```

Edit `/etc/shadow` to prevent root login:

```
# nano /etc/shadow
------------------
root:!*:14871::::::
```

Edit `/etc/mkinitcpio.conf`:

```
# nano /etc/mkinitcpio.conf
---------------------------
HOOKS=(systemd autodetect modconf block filesystems keyboard)
```

Create the `initramfs`:

```
# mkinitcpio -P
```

Install CPU Microcode to be loaded by `grub`:

```
AMD:
# pacman -S amd-ucode

Intel:
# pacman -S intel-ucode
```

Make and save the grub configuration:

```
# grub-mkconfig -o /boot/grub/grub.cfg
```

Install Grub to the MBR:

```
# grub-install /dev/sda
```

Configure the services to start at boot:

Server:
NOTE: Configure [systemd-resolved](https://wiki.archlinux.org/index.php/Systemd-resolved) and [systemd-networkd](https://wiki.archlinux.org/index.php/Systemd-networkd).

```
# systemctl enable sshd.service systemd-timesyncd.service systemd-resolved.service systemd-networkd.service
```

Notebook:

```
# systemctl enable sshd.service systemd-timesyncd.service systemd-resolved.service NetworkManager.service gdm.service cups.service
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
