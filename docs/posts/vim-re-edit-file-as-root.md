---
title: Vim re-edit file as root
date: 2012-10-10
authors:
    - jorge
categories:
    - Linux
---
Have you ever found yourself in a situation where you’ve edited a file for quite some time, only to find out that you get a “Permission Error” when trying to save the changes? If **vim** is your editor, and you have **sudo**-privileges, try doing this:

```
:w !sudo tee %
```

Does the trick. :)