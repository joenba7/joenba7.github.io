---
title: Firefox 3 and Java
date: 2008-08-18
authors:
    - jorge
categories:
    - Linux
---
One of the things that has been annoying me for ages is getting Firefox and Java working together. For some bizarre reason I never seem to get it working together properly. Till just now. Here’s the easy way of installing Java on your machine, and having it working with Firefox.

Proceed to , and press the Download-button. Download the file named *Linux RPM (self-extracting file)*. Once the file is downloaded, su to root and do a *chmod +x* on it.

```
su -
chmod +x jre-6u7-linux-i585.rpm.bin
```

Then run the binary file:

```
./jre-6u7-linux-i585.rpm.bin                                                                                                                                                                                                                                                                               
```

After agreeing to the license terms, Java should install itself. Now we’ll try to make it work with Firefox. Head to */usr/lib/mozilla/plugins/* and remove the symlink named *libjavaplugin.so*. Then proceed to create a new symlink with the same name, only link it to */usr/java/latest/plugin/i386/ns7/libjavaplugin\_oji.so*:

```
cd /usr/lib/mozilla/plugins/
rm libjavaplugin.so
ln -s /usr/java/latest/plugin/i386/ns7/libjavaplugin_oji.so libjavaplugin.so
```

And that’s that. Restart your Firefox if it’s already up and going, and once it is back up, Java should now be working. :)