---
title: xsu
date: 2007-04-24
authors:
    - jorge
categories:
    - Snippets
---
```
#!/bin/sh

# Program to run X-programs as root
# (when you get the "**WARNING** cannot open display" message)

su - -c "exec env DISPLAY='$DISPLAY'
XAUTHORITY='${XAUTHORITY-$HOME/.Xauthority}'
"'"$SHELL"'" -c '$*'"
```