---
title: Select browser from variable DISPLAY
date: 2010-10-20
authors:
    - jorge
categories:
    - Linux
    - Snippets
---
I have two monitors at work, which I like to use in a separate X-screen setup. Meaning that each monitor has an own X-server running on it. Thing is though, I like to use Firefox on Monitor 1, while I use Google Chrome on Monitor 2. So with this script, right-clicking a link in Monitor 1 will open it up in Firefox, while if I do so on Monitor 2, it will open it up in Google Chrome. Very nifty. Just go to **System -&gt; Preferences -&gt; Preferred Applications** (if you’re using GNOME like moi) and select this script instead of a regular browser.

```
#!/bin/bash
# A little script that selects a browser
# to show an URL on, based on the variable
# DISPLAY on a separate X-screen setup.
# In other words, the active monitor.
#
# Jorge Enrique Barrera 

if [ "$DISPLAY" = ":0.0" ]; then
    /usr/bin/firefox $1
elif [ "$DISPLAY" = ":0.1" ]; then
    /usr/bin/google-chrome $1
fi
```