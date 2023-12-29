---
title: Spotify for Linux RPM
date: 2010-09-08
authors:
    - jorge
categories:
    - Linux
---
I’ve taken the liberty of converting the “Spotify for Linux”-files located at  from **.deb** to **.rpm**. For now it can only be used if you have a Premium account.

To install it on Fedora 13, do the following:

```
su -
rpm --nodeps -Uvh http://jorge.fbarr.net/files/spotify-client-gnome-support.noarch.rpm http://jorge.fbarr.net/files/spotify-client-qt.i386.rpm
```

And that’s it. Now you can run Spotify as a Linux-application instead of through Wine. :)