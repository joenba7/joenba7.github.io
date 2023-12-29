---
title: How to join multiple .avi or .mpg files &#8211; Part Two
date: 2009-08-13
authors:
    - jorge
categories:
    - Linux
---
25th of May 2008 I wrote a post about how you could join multiple files that looked something like **movie.avi.001**, **movie.avi.002** and so on. I found a new tool that does the job a bit easier, **lxsplit**. The syntax of **lxsplit** is like so:

```
[jorge@ashitaka ~]$ lxsplit 
LXSplit v0.2.4 by Richard Stellingwerff, O. Sezer.
Home page: http://lxsplit.sourceforge.net/

Usage: lxsplit [OPTION] [FILE] [SPLITSIZE]

Available options:
 -j : join the files beginning with the given name
 -s : split the given file.  requires a valid size
Splitsize examples: 15M, 100m, 5000k, 30000000b

Examples:
    lxsplit -s hugefile.bin 15M
    lxsplit -j hugefile.bin.001
```

So to join all our files, we execute:

```
lxsplit -j movie.avi.001
```

And it will join all files named **movie.avi.00{1,2,3}** and so on. :)