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

FILE_DIR="/home/jorge/backup/"
NUMBER_OF_FILES="$(ls -t $FILE_DIR | wc -l)"
RETAIN=10

if [ "$NUMBER_OF_FILES" -ge "$RETAIN" ]; then
    cd $FILE_DIR
    ls -t | sed -e "1,$RETAIN d" | xargs rm -f
fi
```

This can be modified to match with any type of file or filename.
