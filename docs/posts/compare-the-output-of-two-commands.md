---
title: Compare the output of two commands
date: 2015-11-26
authors:
    - jorge
categories:
    - Snippets
tags:
    - bash
    - comparison
    - diff
    - linux
---
..without creating a temporary file:

```
diff -y <(ls First_directory) <(ls Second_directory)
```

The **-y** is just to show a side-by-side comparison. Very handy! And the result?

```
[jorge@j-laptop temp]$ diff -y <(ls First_directory) <(ls Second_directory)
common.txt							common.txt
first_file.txt						      |	second_file.txt
```

Thanks to my colleague [Ingvar](http://ingvar.blog.redpill-linpro.com/) for showing me this!
