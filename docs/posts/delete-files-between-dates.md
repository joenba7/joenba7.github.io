---
title: Delete files between two dates
date: 2024-01-10
authors:
    - jorge
categories:
    - Snippets
tags:
    - linux
    - delete files
    - dates
---

To delete all files in a directory, between two dates, use the **find**
command:


```bash
find /path/to/dir/ -type f -newermt 2022-01-01 \! -newermt 2023-01-01 -exec rm -fv '{}' +
```
