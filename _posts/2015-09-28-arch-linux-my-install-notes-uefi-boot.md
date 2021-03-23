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
# gdisk /dev/sda
```
__NOTE:__ Make sure to create a __UEFI__ partition.  The partition type code to use in `gdisk` is `EF00`.

This is the how my SSD is partitioned:

```
NAME    SIZE    MOUNTPOINT
sda     500.0G
├─sda1  128.0M  part /boot/efi
├─sda2  491.9G  part /,/home/
└─sda3    8.0G  part [SWAP]
```

If you created a swap partition, setup the swap space and enable it:

```
# mkswap -L SWAP /dev/sda3
# swapon LABEL=SWAP
```

Make the FAT32 filesystem on the UEFI patition:

```
# mkfs.fat -F32 -n UEFI /dev/sda1
```

Make the BTRFS on the partition for Arch Linux:

```
# mkfs.btrfs -L ArchLinux /dev/sda2
```

Create BTRFS subvolumes:

```
# mount LABEL=ArchLinux /mnt
# btrfs subvolume create /mnt/@arch
# btrfs subvolume create /mnt/@home
# umount /mnt
```

Mount the subvolumes:

```
# mount -o subvol=@arch LABEL=ArchLinux /mnt
# mkdir /mnt/home
# mount -o subvol=@home LABEL=ArchLinux /mnt/home
```

Mount the UEFI partition:

```
# mkdir -p /mnt/boot/efi
# mount LABEL=UEFI /mnt/boot/efi
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
# useradd --home-dir /home/leedev --create-home --groups users,wheel,sys --shell /bin/bash leedev
```

Set the password for this new user:

```
# passwd leedev
```

Install a lean but complete Gnome desktop environment:

Server:

```
# pacman -S base-devel tmux openssh rsync bash-completion git grub efibootmgr btrfs-progs ecryptfs-utils
```

Notebook:

```
# pacman -S base-devel tmux sudo openssh rsync bash-completion git grub efibootmgr btrfs-progs ecryptfs-utils sysstat gdm gnome-shell gnome-terminal nautilus gnome-control-center gnome-tweak-tool ttf-liberation system-config-printer gnome-backgrounds gnome-keyring gnome-disk-utility baobab gnome-screenshot cheese evince epiphany eog totem mpv geany file-roller networkmanager gst-libav libmtp ttf-freefont noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra libva-vdpau-driver libdvdcss pkgfile xorg-server xorg-xinit libvdpau xf86-video-amdgpu xf86-input-libinput mesa-vdpau mesa-libgl gst-plugins-ugly xf86-input-libinput gimp inkscape cups ghostscript gsfonts foomatic-db foomatic-db-engine foomatic-db-nonfree seahorse os-prober
```

Give the `wheel` group `sudo` permissions:

```
# echo "%wheel ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/group_wheel
```

Edit `/etc/shadow` to prevent root login:

```
# nano /etc/shadow
------------------
root:!!:14871::::::
```

Edit `/etc/mkinitcpio.conf`:
NOTE: For a server, read [RAID](https://wiki.archlinux.org/index.php/RAID).

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

Install Grub to the UEFI filesystem and add to the UEFI Boot Manager:

```
# grub-install
```
NOTE: Use argument `--target=i386-efi` if you have 32bit UEFI.

Edit `/etc/default/grub`:

```
# nano /etc/default/grub
------------------------
GRUB_TIMEOUT=1
GRUB_CMDLINE_LINUX_DEFAULT=""
```

Make and save the grub configuration:

```
# grub-mkconfig -o /boot/grub/grub.cfg
```

Configure the services to start at boot:

Server:
NOTE: Configure [systemd-resolved](https://wiki.archlinux.org/index.php/Systemd-resolved) and [systemd-networkd](https://wiki.archlinux.org/index.php/Systemd-networkd).

```
# systemctl enable sshd.service systemd-timesyncd.service systemd-networkd.service systemd-resolved.service
```

Notebook:

```
# systemctl enable sshd.service systemd-timesyncd.service gdm.service NetworkManager.service  cups.service
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
