---
title: while read
date: 2024-01-17
authors:
    - jorge
categories:
    - Snippets
tags:
    - linux
    - while
    - read
    - eof
    - paste
---

More than once I find myself opening a file with vim, pasting the contents,
saving the file, and then going back to bash to do some magic with it. With
this line, you simply run the command, paste in the contents that you want, and
end with **EOF** to end the loop. In this case, I paste in a list of URLs, but
only want to grab those that contain the string 'https':

<!-- more -->

```bash
while read LINE; do echo $LINE | grep https; done << EOF   
```

..which looks a little something like this:


```bash
[jorge@jeb-desktop ~]$ while read LINE; do echo $LINE | grep https; done << EOF
http://example.org/
https://authority.example.com/
https://www.example.com/                                                                                                http://example.com/
EOF
https://authority.example.com/
https://www.example.com/ http://example.com/
```
