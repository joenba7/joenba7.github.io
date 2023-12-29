---
title: Mount iso
date: 2007-06-02
authors:
    - jorge
categories:
    - Snippets
    - ''
---
```
mount -t iso9660 -o ro,loop=/dev/loop0 /var/tmp/some.iso /mnt/cdrom
```