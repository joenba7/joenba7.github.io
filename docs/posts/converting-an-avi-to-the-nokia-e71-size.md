---
title: Converting an avi to the Nokia E71 size
date: 2009-07-24
authors:
    - jorge
categories:
    - Snippets
---
This is a oneliner to convert an avi-file to the correct dimensions of a Nokia E71. The screen is small, but sure beats to buy an iTouch if your cell already can play movies for you. :)

```
ffmpeg -y -i input.avi -acodec libfaac -ab 72k -s 320x176 -aspect 16:9 -vcodec libx264 -b 300k -qcomp 0.6 -r 25 -qmin 16 -qmax 51 -qdiff 4 -flags +loop -cmp +chroma -subq 7 -refs 6 -g 250 -keyint_min 25 -rc_eq 'blurCplx^(1-qComp)' -sc_threshold 40 -me_range 12 -i_qfactor 0.71 -directpred 3 output.mp4
```
