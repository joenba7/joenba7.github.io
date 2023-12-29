---
title: Reducing PDF file-size in Linux
date: 2012-11-29
authors:
    - jorge
categories:
    - Linux
---
The other day I downloaded a PDF that ended up being a whole lot bigger than I thought. A “whopping” 230MB, which is another deal compared to the 30MB PDF’s that I’m accustomed to. So how to reduce the file-size? **Ghostscript** to the rescue!

If you have **Ghostscript** installed, run the following command to reduce the file-size of your PDF:

```
gs -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/screen -sOutputFile=new_file.pdf original_file.pdf
```

Now you have a few options here under **-dPDFSettings**:

- **/screen** selects low-resolution output, and the lowest file-size.
- **/ebook** selects medium-resolution output, with a medium file-size.
- **/printer** and /prepress are both the high-resolution options, which is mainly used for printing PDFs. As you might have guessed, this option gives you the biggest file-size (yes, even bigger than your mother).

The results?

```
[jorge@(ashitaka):~/Downloads/PDF] du -h *
29M new_file.pdf
230M original_file.pdf
```

What can I say. I’m a cheap bastard when it comes to storage space, no matter how low the price might be. ;)