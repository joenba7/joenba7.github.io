---
title: urxvt and urlLauncher not working
date: 2013-02-02
authors:
    - jorge
categories:
    - Linux
---
I recently found myself having problems with urxvt. In my configuration-file, **.Xdefaults**, I had the following lines set up:

```
URxvt.perl-ext-common : default,matcher
URxvt.urlLauncher : /usr/bin/google-chrome
```

This would enable urxvt to highlight a link in white, and when clicking it would launch the URL in to a tab of my browser of choice. But one day it suddenly stopped working, for no apparent reason. After a little digging, I found the following in urxvt’s changelog, http://cvs.schmorp.de/rxvt-unicode/Changes:

> – INCOMPATIBLE CHANGE: renamed urlLauncher resource to url-launcher.

Changing **URxvt.urlLauncher** to **URxvt.url-launcher** in **.Xdefaults** seemed to fix the problem!