---
title: Wallpaper concatenation with ImageMagick
date: 2011-04-03
authors:
    - jorge
categories:
    - Linux
---
At both home and work, I have a dual monitor setup. Having recently discovered the windowmanager **xmonad**, I’ve found a setup that enables me to use **Xinerama**, and at the same time have the functionality that a separate X screen-setup would provide me. But with Xinerama comes the Twin View-setting that the Nvidia drivers provide, which “merges” both monitors, and gives me one large virtual desktop. Finding wallpaper for it can be a bit tricky, especially the ones that are designed for one specific resolution. But this is where **ImageMagick** comes into play. What I want is two images, with different resolutions, side by side. You could of course use something else, like **GIMP**, but you’d have to align things manually, and why risk it when a simple command can do the trick?

First install **ImageMagick**:

```
yum install ImageMagick
```

..then do the magic:

```
montage wallpaper_1920x1080.jpg wallpaper_1680x1050.jpg -tile 2x1 -geometry "1920x1080+0+0" -mode concatenate final_wallpaper.jpg
```

By setting **geometry** to “**1920×1080+0+0**”, we set the preferred size of one of the tiles to be that, thus resizing images that are too large.

The results? Well, for me, this was it:

[![xmonad_wallpaper](http://jorge.fbarr.net/wp-content/uploads/2013/02/xmonad_wallpaper-1024x307.jpg)](http://jorge.fbarr.net/wp-content/uploads/2013/02/xmonad_wallpaper.jpg)

Enjoy!