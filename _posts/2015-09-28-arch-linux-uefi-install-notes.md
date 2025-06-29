---
layout: post
title: 'Arch Linux: UEFI Install Notes'
date: '2015-09-28 17:00:42'
---

Boot the Arch Linux ISO using UEFI.

Change shell to bash and reload shell:

```
# usermod -s /bin/bash root
# exit
```

Partition your storage device with GUID Partition Table (GPT) partition table:

```
# gdisk /dev/nvme0n1
```
__NOTE:__ Make sure to create a __UEFI__ partition.  The partition type code to use in `gdisk` is `EF00`.

This is the how my SSD is partitioned:

```
NAME    SIZE    MOUNTPOINT
nvme0n1     259:0    0  238.5G  0 disk
├─nvme0n1p1 259:4    0      1G  0 part
├─nvme0n1p2 259:5    0     99G  0 part
└─nvme0n1p3 259:6    0  130.5G  0 part
└─nvme0n1p4 259:7    0    8.0G  0 part
```

If you created a swap partition, setup the swap space and enable it:

```
# mkswap /dev/nvme0n1p4
# swapon /dev/nvme0n1p4
```

Make the FAT32 filesystem on the UEFI patition:

```
# mkfs.fat -F32 /dev/nvme0n1p1
```

Make the BTRFS on the partition for Arch Linux:

```
# mkfs.btrfs /dev/nvme0n1p2
```

Create BTRFS subvolumes:

```
# mount /dev/nvme0n1p2 /mnt
# btrfs subvolume create /mnt/@arch
# btrfs subvolume create /mnt/@home
# btrfs subvolume create /mnt/@var
# umount /mnt
```

Mount the subvolumes:

```
# mount -o subvol=@arch /dev/nvme0n1p2 /mnt
# mkdir /mnt/home
# mount -o subvol=@home /dev/nvme0n1p2 /mnt/home
# mkdir /mnt/var
# mount -o subvol=@var /dev/nvme0n1p2 /mnt/var
```

Mount the UEFI partition:

```
# mkdir /mnt/boot
# mount /dev/nvme0n1p1 /mnt/boot
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
-----------------
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

Create a group and user:

```
# groupadd --gid 1000 leedev
# useradd --home-dir /home/leedev --create-home --uid 1000 --gid 1000 --groups wheel,sys --shell /bin/bash leedev
```

Set the password for this new user:

```
# passwd leedev
```

Install a lean but complete Server or Gnome desktop environment:

Server:

```
# pacman -S tmux sudo openssh rsync bash-completion git btrfs-progs ecryptfs-utils efibootmgr
```

Notebook:

```
# pacman -S tmux sudo openssh rsync bash-completion git btrfs-progs ecryptfs-utils base-devel sysstat gdm gnome-shell gnome-terminal nautilus gnome-control-center gnome-tweaks ttf-liberation system-config-printer gnome-backgrounds gnome-keyring gnome-disk-utility baobab gnome-screenshot cheese evince epiphany eog totem mpv geany file-roller networkmanager dhclient gst-libav libmtp ttf-freefont noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra libva-vdpau-driver libdvdcss pkgfile xorg-server xorg-xinit libvdpau xf86-video-amdgpu xf86-input-libinput mesa-vdpau mesa-libgl gst-plugins-ugly xf86-input-libinput gimp inkscape cups ghostscript gsfonts foomatic-db foomatic-db-engine foomatic-db-nonfree seahorse efibootmgr
```

Give the `wheel` group `sudo` permissions:

```
# nano /etc/sudoers.d/group_wheel
---------------------------------
Defaults:%wheel runcwd=*
%wheel ALL=(ALL) NOPASSWD: ALL
```

Prevent root login:

```
# passwd --lock root
```

Install CPU Microcode:
```
AMD:
# pacman -S amd-ucode

Intel:
# pacman -S intel-ucode
```

Edit `/etc/mkinitcpio.conf`:
NOTE: For a server, read [RAID](https://wiki.archlinux.org/index.php/RAID).

```
# nano /etc/mkinitcpio.conf
---------------------------
HOOKS=(systemd autodetect microcode modconf block filesystems keyboard)
```

Create the `initramfs`:

```
# mkinitcpio -P
```

Install bootloader:

```
# bootctl install
```

Get UUID of `/` partition:

```
# blkid /dev/nvme0n1p2
/dev/nvme0n1p2: UUID="5e023168-f275-422e-b87f-566735100b37" UUID_SUB="1094b132-ab0a-48e5-a772-a7d092f0f66b" BLOCK_SIZE="4096" TYPE="btrfs" PARTLABEL="Linux filesystem" PARTUUID="70d16fe2-666f-4390-af97-dab0fea07583"
```

Create boot entries:

```
# nano /boot/loader/entries/arch.conf
-------------------------------------
title   Arch Linux
linux   /vmlinuz-linux
initrd  /intel-ucode.img
initrd  /initramfs-linux.img
options root=UUID=5e023168-f275-422e-b87f-566735100b37 rw rootflags=subvol=@arch mitigations=off
```

```
# nano /boot/loader/entries/arch-fallback.conf
----------------------------------------------
title   Arch Linux (fallback initramfs)
linux   /vmlinuz-linux
initrd  /intel-ucode.img
initrd  /initramfs-linux-fallback.img
options root=UUID=5e023168-f275-422e-b87f-566735100b37 rw rootflags=subvol=@arch mitigations=off
```

Setup `loader.conf`:

```
# nano /boot/loader/loader.conf
-------------------------------
default  arch.conf
timeout  1
console-mode max
editor   no
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
