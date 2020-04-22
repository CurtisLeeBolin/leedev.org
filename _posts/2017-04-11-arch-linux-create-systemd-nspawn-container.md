---
layout: post
title: 'Arch Linux: Create Default systemd-nspawn Container for Cloning'
date: '2017-04-11 15:17:23'
---

Create container subvolume:

```
# btrfs subvolume create /var/lib/machines/default
```
Bootstrap the container:

```
# pacstrap -i -c -d /var/lib/machines/default base --ignore linux --ignore linux-firmware
```

Start the container:

```
# machinectl start default
```

Open a root shell in the container:

```
# machinectl shell default
```

Setup networking:
[Random Mac Address](https://www.hellion.org.uk/cgi-bin/randmac.pl?scope=global&oui=00%3A16%3A3e&type=unicast)

```
# nano /etc/systemd/network/mv-mvl0.network
-------------------------------------------
[Match]
Name=mv-mvl0

[Link]
MACAddress=00:16:3e:8c:7d:f8

[Network]
DHCP=yes
LinkLocalAddressing=no
```

```
# rm /etc/resolv.conf
# ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
# systemctl enable --now systemd-networkd.service systemd-resolved.service
```

Configure and generate locales:

```
# echo "LANG=en_US.UTF-8" > /etc/locale.conf
# echo "en_US.UTF-8 UTF-8" > /etc/locale.gen
# export LANG=en_US.UTF-8
# locale-gen
```

Create a user:

```
# useradd --home-dir /home/leedev --create-home --gid users --groups wheel,sys --shell /bin/bash leedev
```

Set the password for this new user:

```
# passwd leedev
```

Install a few things:

```
# pacman -S tmux sudo rsync bash-completion git
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

Switch to user `leedev`:

```
# su leedev
```

Git clone `MyArchLinuxGoodies`:

```
$ mkdir Projects
$ cd Projects
$ git clone https://github.com/CurtisLeeBolin/MyArchLinuxGoodies.git
```

Install yaourt:

```
$ cd MyArchLinuxGoodies/scripts
$ ./install_yaourt.sh
```

Add default settings:

```
$ ./addDefaultSettings.sh
```

Poweroff Container:
```
# systemctl poweroff
```