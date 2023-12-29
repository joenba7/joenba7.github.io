---
title: Create a DVD with growisofs
date: 2007-07-08
authors:
    - jorge
categories:
    - Snippets
---
```
#!/bin/sh

# Create an AUDIO_TS subdirectory if it does not exist
[ ! -d AUDIO_TS ] && mkdir AUDIO_TS

chown -R root:root AUDIO_TS VIDEO_TS
chmod 500 AUDIO_TS VIDEO_TS
chmod 400 VIDEO_TS/*

growisofs -dvd-compat -Z /dev/dvd -R -J -dvd-video .
```