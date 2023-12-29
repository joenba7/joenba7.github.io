---
title: Mac &#8211; Play DVD automatically on insertion in VLC Media Player
date: 2008-04-15
authors:
    - jorge
categories:
    - Apple
---
So I’ve installed Mac OS X 10.5 on my PowerBook G4 12?, and it’s working quite well so far. Until the native DVD-playing software went haywire that is. Needless to say, I turned to VLC. Now the problem is getting VLC to automatically launch and start playing the DVD when a DVD is inserted. Here’s what you do. Open up the program *Script Editor*, and place the following in there:

```
tell application "VLC"
OpenURL "dvdnav:///dev/rdisk1"
play
next
end tell

```

Save it, and close the application. Now go to *System Preferences* -&gt; *CDs &amp; DVDs* and when pressing *When you insert a video DVD*, select *Run script…* and just use the applescript you just made. Voila, it works!

