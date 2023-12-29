---
title: Firefox and Java on 64 bit Linux
date: 2009-07-08
authors:
    - jorge
categories:
    - Linux
---
Wondering what you need to do in order to install Java on a 64 bit Linux installation? Few and simple steps really.

First head over to the Download-section at . Select to download the file named **Linux x64 RPM** (if you’re using an RPM-based distribution like Fedora). Once it’s downloaded, and assuming it has been downloaded to the folder **Download**, do the following:

```
After agreeing to the license terms, just type Yes and press enter. Now making the plugin work with Firefox. Just execute the following command:
ln -s /usr/java/latest/lib/amd64/libnpjp2.so /usr/lib64/mozilla/plugins/

Cross your fingers, restart Firefox, and things should hopefully be working. :) Head over here to check out if it worked.
Good luck!
```