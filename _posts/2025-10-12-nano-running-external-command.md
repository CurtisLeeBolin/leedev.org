---
layout: post
title: 'nano: Running External Command'
date: '2025-10-12 15:23:09'
---

## Ctrl + T

Replacing `\n` with `' '`:

```
|sed -z 's/\n/ /g'
```

<br>

Replacing `' '` with `\n`:

```
|sed -z 's/ /\n/g'
```
