---
layout: post
title: 'Arch Linux: trusting gpg signatures for makepkg'
date: '2015-09-22 19:53:53'
---

When trying to build a package with `makepkg`, you might get a PGP signature verification error.

Example with Linus Torvalds signature missing with the package `linux`:
```
$ makepkg -s
==> Making package: linux 4.2-4 (Tue Sep 22 14:40:55 CDT 2015)
==> Checking runtime dependencies...
==> Checking buildtime dependencies...
==> Retrieving sources...
  -> Found linux-4.2.tar.xz
  -> Found linux-4.2.tar.sign
  -> Found config
  -> Found config.x86_64
  -> Found linux.preset
  -> Found change-default-console-loglevel.patch
  -> Found 0001-make_flush_workqueue_non_gpl.patch
  -> Found 0001-e1000e-Fix-tight-loop-implementation-of-systime-read.patch
  -> Found 0001-netfilter-conntrack-use-nf_ct_tmpl_free-in-CT-synpro.patch
  -> Found 0001-fix-bridge-regression.patch
==> Validating source files with sha256sums...
    linux-4.2.tar.xz ... Passed
    linux-4.2.tar.sign ... Skipped
    config ... Passed
    config.x86_64 ... Passed
    linux.preset ... Passed
    change-default-console-loglevel.patch ... Passed
    0001-make_flush_workqueue_non_gpl.patch ... Passed
    0001-e1000e-Fix-tight-loop-implementation-of-systime-read.patch ... Passed
    0001-netfilter-conntrack-use-nf_ct_tmpl_free-in-CT-synpro.patch ... Passed
    0001-fix-bridge-regression.patch ... Passed
==> Verifying source file signatures with gpg...
    linux-4.2.tar ... FAILED (unknown public key 79BE3E4300411886)
==> ERROR: One or more PGP signatures could not be verified!
```

The signature must be trusted by your user, not by root using the `pacman-key` command.  For this use the `gpg` command.

To download the missing signature:
```
$ gpg --recv-keys 79BE3E4300411886
gpg: /home/lee/.gnupg/trustdb.gpg: trustdb created
gpg: key 00411886: public key "Linus Torvalds <torvalds@linux-foundation.org>" imported
gpg: no ultimately trusted keys found
gpg: Total number processed: 1
gpg:               imported: 1
```

Now you can build without the error.