---
layout: post
title: 'Linux: xhost'
date: '2018-05-22 16:12:09'
---

### NOTE: This is unsafe
# Temporarily disable X server's access control.
## Disable for only local:

```
$ xhost +local:
non-network local connections being added to access control list
```

## Disable for any host:

```
$ xhost +
access control disabled, clients can connect from any host
```

# Run an X program as another user.

```
$ sudo -u gdm dbus-launch gnome-control-center
```

## If you also need to change the user's shell.

```
$ sudo -u gdm -s /bin/bash -c "dbus-launch gnome-control-center"
```


# Renable X server's access control.
## Only local

```
$ xhost -local:
non-network local connections being removed from access control list
```

## Any host

```
$ xhost -
access control enabled, only authorized clients can connect
```
