---
title: Remove comments and empty lines with grep
date: 2008-10-28
authors:
    - jorge
categories:
    - Linux
    - Snippets
---
```
grep -v -e ^# -e ^$ file.txt
```