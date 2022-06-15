---
layout: post
title: 'Arch Linux: mdadm grow ppl'
date: '2022-06-09 16:35:33'
---

RAID5 with ppl consistency policy can't be grown without more steps.

Stop the array:

```
# mdadm --stop /dev/md0
```

Assemble the array with `no-ppl`:

```
# mdadm --assemble /dev/md0 --update=no-ppl /dev/sdg1 /dev/sde1 /dev/sdh1 /dev/sdf1 /dev/sdc1
```

Add the new drive:

```
# mdadm --add /dev/md0 /dev/sdd1
```

Grow the array and set consistency policy back to ppl :

```
# mdadm --grow /dev/md0 --consistency-policy=ppl --raid-devices=6
```
