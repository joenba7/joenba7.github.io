---
title: Scan for newly added disks
date: 2023-12-20
authors: 
    - jorge
categories:
    - Snippets
---

To scan for newly added/modified disks, run the following command as root:

```bash
for f in /sys/class/scsci_host/*; do echo "---" > $f/scan; done
```
