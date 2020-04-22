---
layout: post
title: 'Arch Linux: journald notes'
date: '2015-10-14 05:56:15'
---

```
# journalctl --boot=0 --priority=3 --output=json-pretty
```
"emerg" (0), "alert" (1), "crit" (2), "err" (3), "warning" (4), "notice" (5), "info" (6), "debug" (7)
