---
title: flac to mp3
date: 2009-05-25
authors:
    - jorge
categories:
    - Linux
    - Snippets
---
```
for file in *.flac; do $(flac -cd "$file" | lame -h - "${file%.flac}.mp3"); done 
```