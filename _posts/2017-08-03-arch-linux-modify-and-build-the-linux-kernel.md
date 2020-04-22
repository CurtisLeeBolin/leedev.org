---
layout: post
title: 'Arch Linux: Modify and Build the Linux Kernel'
date: '2017-08-03 12:55:58'
---

Change to your projects directory:
```
$ cd ~/Projects/
```

Retrieve package files:

  * If you are wanting to build the lasted kernel from the `testing` repository, change `linux` to `testing/linux` in the following command.

```
$ asp export linux
```

Change to the package directory:
```
$ cd linux
```

Uncomment `make nconfig` in `PKGBUILD`:

  * This will cause makepkg to run `make nconfig`, so you can configure the changes you want to the linux kernel before it builds.

```
$ nano PKGBUILD
------------------------------------------------
  make nconfig # new CLI menu for configuration
```

Now build the package:

  * `-s` argument installs dependencies needed to build the linux kernel.
  *  Follow [these instructions](/arch-linux-adding-gpg-keys-for-makepkg/) to add any missing trusted gpg keys.

```
$ makepkg -s
```

`makepkg` will download the needed files, apply all patches, run `make nconfig`, then build the kernel.

Install the package you built:
```
# pacman -U linux-4.12.4-1-x86_64.pkg.tar
```