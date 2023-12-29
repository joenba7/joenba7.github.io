---
title: Vim &#8211; Commenting out multiple lines
date: 2011-01-17
authors:
    - jorge
categories:
    - Linux
    - Snippets
---
There are two ways to do this without a **vim** plugin:

1. Select your lines with **VISUAL BLOCK (CTRL-V)**, then press **I** to insert before all highlighted lines. Next type your comment character (**\#**, **;**, depending on the language), then press **ESC** twice.

```
s/^/#
```

In other words, substitute the selected start of lines with the **\#** character.