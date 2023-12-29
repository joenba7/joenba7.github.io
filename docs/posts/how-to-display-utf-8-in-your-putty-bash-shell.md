---
title: How to display UTF-8 in your Putty bash shell
date: 2008-03-14
authors:
    - jorge
categories:
    - Linux
    - Windows
---
My girlfriend was having some problems displaying the correct characters while talking on IRC through irssi, and putty. Eventually I came to the conclusion that it wasn’t my server or irssi that was configured badly. Searching the internet I found this:

```
echo -ne 'e%Ge[?47he%Ge[?47l'
```

Simply put that into your .bash\_profile or .bashrc and relog to your server. It’s a collection of terminal escape codes that together means ESC %G – “switch to UTF-8”.