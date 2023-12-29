---
title: Installing add-ons globally on Firefox
date: 2009-06-21
authors:
    - jorge
categories:
    - Linux
---
Most often when creating different profiles in Firefox, you use them for various things. One profile might have all the web-developer tools and add-ons you need in order to be your geeky self, while profile number two has your gamer-add-ons, which you don’t want to mix. But what if you want certain add-ons to be global? So when you do create a second profile, you won’t have to reinstall the same add-on twice, or more. The answer? Installing things through the command line.

For instance, let’s take the add-on [AdBlock Plus](http://adblockplus.org). Great add-on. I for one want it on every Profile. So instead of doing it through the browser-interface, installing it once for every profile, do it through the command line, globally.

Become **root** and grab the add-on:

```
And then install it globally:
firefox -install-global-extension /root/adblock_plus-1.0.2.xpi

And presto, the addon should now be located in /usr/lib/firefox/extensions, available for all profiles.
Nifty huh?
```