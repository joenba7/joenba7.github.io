---
title: How to join multiple .avi or .mpg files
date: 2008-05-25
authors:
    - jorge
categories:
    - Linux
    - Snippets
---
Imagine that you for some reason end up having several files, such as *a.movie.avi.001*, *a.movie.avi.002*, and so on. How do you then join them up into one big avi file? The answer is actually pretty simple – *cat* and *mencoder*.

Do the following if you don’t have *mencoder* installed:

```
# yum install mencoder mplayer

```

Then:

```
$ cat a.movie.avi.001 a.movie.avi.002 a.movie.avi.003 > a.movie.avi

```

That’s pretty much it, but there’s one final step before it’s done.

```
$ mencoder -forceidx -oac copy -ovc copy a.movie.avi -o a.movie.final.avi

```

And you’re done. :)

