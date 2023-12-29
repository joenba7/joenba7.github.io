---
title: Embedding subtitles srt into avi files with mencoder
date: 2010-03-17
authors:
    - jorge
categories:
    - Snippets
---
Easier than you might think.

```
mencoder -sub movie_subtitle.srt -ovc xvid -xvidencopts bitrate=-700000 -oac copy -o new.movie.name.avi movie.avi
```
