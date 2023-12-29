---
title: Removing your Master Boot Record (MBR)
date: 2009-11-26
authors:
    - jorge
categories:
    - Linux
---
Removing MBR (including the partition table):

```
dd if=/dev/null of=/dev/sdX bs=512 count=1
```

Removing MBR (excluding the partition table):

```
dd if=/dev/null of=/dev/sdX bs=446 count=1
```

**sdX** is the device you want to remove the MBR of (like a USB-stick).