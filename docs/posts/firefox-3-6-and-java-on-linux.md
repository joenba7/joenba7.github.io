---
title: Firefox 3.6 and Java on Linux
date: 2010-03-17
authors:
    - jorge
categories:
    - Linux
---
Finding that you’re lacking Java in Firefox 3.6 on Linux? The steps to install it are easy, but you have to do things a bit differently than you have in previous Firefox-versions.

First head over to the Download-section at http://www.java.com. Select to download the file named Linux RPM (self-extracting file) (if you’re using an RPM-based distribution like Fedora). Once it’s downloaded, and assuming it has been downloaded to the folder **Downloads**, do the following:

```
su -
chmod +x /home/jorge/Downloads/jre-6u18-linux-i586-rpm.bin
/home/jorge/Downloads/jre-6u18-linux-i586-rpm.bin
```

After agreeing to the license terms, just type **Yes** and press enter. Now making the plugin work with Firefox. I mentioned earlier that you have to do things a bit differently. Whereas we used the file **libjavaplugin**.so before, now we have to use the next generation Java plugin, namely **libnpjp2.so**. So execute the following command to make things work:

```
ln -s /usr/java/latest/lib/i386/libnpjp2.so /usr/lib/mozilla/plugins/
```

Restart your browser, and you should have Java. Verify it at http://java.com/en/download/installed.jsp?detect=jre&amp;try;=1.

Enjoy!