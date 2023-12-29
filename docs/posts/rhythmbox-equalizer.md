---
title: Rhythmbox equalizer
date: 2009-06-30
authors:
    - jorge
categories:
    - Linux
---
**Update:** For a newer and updated equalizer-plugin, check out the post [Rhythmbox Equalizer Revisited](http://jorge.fbarr.net/2010/02/18/rhythmbox-equalizer-revisited/). Everything below here is outdated.

For a while I’ve been using **audacious** to play my music. No particular reason, other than that it has an equalizer. I’ve heard lots of good things about **Rhythmbox** though, but lacking an equalizer, I never turned to it. But now, thanks to **Teemu Kallio** from , I at least am going to start using **Rhythmbox**.

Grab the file from . I used to link to the main site, but it seems that it has been taken down.

So, the installation:

```
wget http://jorge.fbarr.net/files/rhythmbox-eq.tar.gz
mkdir -p ~/.gnome2/rhythmbox/plugins/
tar -zxvf rhythmbox-eq.tar.gz -C ~/.gnome2/rhythmbox/plugins/
```

And that’s pretty much it. Fire up **Rhythmbox**, go to **Edit** -&gt; **Plugins** and select **Equalizer**. Now press the **Configure**-button to configure it. If you want the **Rock**-preset that you usually find in **audacious** and/or **xmms**, make the equalizer have the same values as in the screenshot below:

[![rhythmbox-equalizer](http://jorge.fbarr.net/wp-content/uploads/2013/02/rhythmbox-equalizer.png)](http://jorge.fbarr.net/wp-content/uploads/2013/02/rhythmbox-equalizer.png)

A big thanks to **Teemu Kallio**. :)

