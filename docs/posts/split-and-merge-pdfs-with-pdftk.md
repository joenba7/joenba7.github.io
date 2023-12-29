---
title: Split and merge PDFs with pdftk
date: 2010-01-20
authors:
    - jorge
categories:
    - Linux
---
**Update:** Unfortunately, **pdftk** seems [no longer to be maintained for Fedora 21](https://bugzilla.redhat.com/show_bug.cgi?id=1033302). So if you’re having problems getting hold of it, try the alternative utility **poppler-utils**. You can find the guide at [http://jorge.fbarr.net/2015/02/20/split-and-merge-pdfs-part-2/](http://jorge.fbarr.net/2015/02/20/split-and-merge-pdfs-part-2/ "Split and merge PDFs - part 2").

First install it:

```
yum -y install pdftk
```

Then use it:

Merge
-----

```
pdftk 1.pdf 2.pdf 3.pdf cat output 123.pdf
```

Split
-----

```
pdftk mydoc.pdf burst
```

A very handy tool.