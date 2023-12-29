---
title: Want to try out Chromium in Fedora 11?
date: 2009-08-28
authors:
    - jorge
categories:
    - Linux
---
Easy peasy.

First we need to create a file called **chromium.repo** in **/etc/yum.repos.d/** and put the following content in it:

```
[chromium]
name=Chromium Test Packages
baseurl=http://spot.fedorapeople.org/chromium/F$releasever/
enabled=1
gpgcheck=0
```

Save and close the file. When done, execute the following command:

```
yum install chromium
```

And you’re done. :)

Getting Flash to work with Chromium on a 64-bit system
------------------------------------------------------

It’s actually not as hard as you think:

```
wget http://download.macromedia.com/pub/labs/flashplayer10/libflashplayer-10.0.32.18.linux-x86_64.so.tar.gz
cd /usr/lib64/chromium-browser/plugins/
tar -zxvf /root/libflashplayer-10.0.32.18.linux-x86_64.so.tar.gz
```

Now start up Chromium with:

```
chromium-browser --enable-plugins
```

Works great!