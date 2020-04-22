---
layout: post
title: 'Linux: tmux - detach remote client'
date: '2017-10-04 17:34:29'
---

I often attach to a `tmux` sessions from multiple computers.  `tmux` will limit the terminal size to the smallest client connected.  The smaller terminal client must be detached to not be limited in the larger terminal.

List the clients:
```
$ tmux list-clients   
/dev/pts/2: 0 [90x49 screen] (utf8)          
/dev/pts/6: 0 [274x69 screen] (utf8)
```

Detach the one/s limiting your screen size:
```
$ tmux detach -t /dev/pts/2 
```