---
title: Retain X amount of files
date: 2024-01-16
authors:
    - jorge
categories:
    - Snippets
tags:
    - linux
    - retain
    - files
    - delete
---

I encountered an issue today where someone requested that we keep the 10 newest
backup files, while the rest of them (the oldest) were deleted. So I made this
little script:


```bash
#!/bin/bash

BACKUP_DIR="/home/jorge/backup/"
BACKUP_NUMBER="$(ls -t $BACKUP_DIR | wc -l)"
RETAIN=10

if [ "$BACKUP_NUMBER" -ge "$RETAIN" ]; then
    ls -t $BACKUP_DIR | sed -e "1,$RETAIN d" | xargs rm -f
fi
```

This can be modified to match with any type of file or filename.
