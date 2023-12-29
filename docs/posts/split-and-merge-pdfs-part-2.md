---
title: Split and merge PDFs &#8211; part 2
date: 2015-02-20
authors:
    - jorge
categories:
    - Linux
tags:
    - merge
    - pdf
    - poppler-utils
    - split
    - tool
    - tutorial
---
A while ago I wrote a post named [Split and merge PDFs with pdftk](http://jorge.fbarr.net/2010/01/20/split-and-merge-pdfs-with-pdftk/). Unfortunately, **pdftk** seems [no longer to be maintained for Fedora 21](https://bugzilla.redhat.com/show_bug.cgi?id=1033302). So we have to use another alternative – **poppler-utils**:

Install the package with:

```
yum install poppler-utils
```

..and you can use them to both split and merge files. **poppler-utils** contains a list of tools that you can use to manipulate PDFs:

- pdfdetach — lists or extracts embedded files (attachments)
- pdffonts — font analyzer
- pdfimages — image extractor
- pdfinfo — document information
- pdfseparate — page extraction tool
- pdftocairo — pdf to png/jpeg/pdf/ps/eps/svg converter using cairo
- pdftohtml — pdf to html converter
- pdftoppm — pdf to ppm/png/jpeg image converter
- pdftops — pdf to postscript (ps) converter
- pdftotext — text extraction
- pdfunite — document merging tool

So, we move on to merging and splitting:

Merge
-----

```
pdfunite first_page.pdf second_page.pdf end_document.pdf
```

Split
-----

```
pdfseparate -f 1 end_document.pdf %d.pdf
```

Check out **man pdfseparate** for more information about the tool.
