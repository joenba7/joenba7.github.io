---
title: One Time Password Calculator on Fedora
date: 2008-12-02
authors:
    - jorge
categories:
    - Linux
---
I have been annoyed SO many times of the lack of OTP-Calculators in Fedora. With Debian and Ubuntu you can find the program donkey. Unfortunately there’s nothing in sight for our beloved distribution. I managed to convert the donkey debian package to an rpm. Here’s how you do it.

First off we need to install **alien**, a package converter that converts between Red Hat rpm, Debian deb, Stampede slp, Slackware tgz, and Solaris pkg file formats.

Download the [Deb-rpm package](http://content.hccfl.edu/pollock/AUnix1/alien/deb-1.10.27-3.i586.rpm) and [html2text](http://content.hccfl.edu/pollock/AUnix1/alien/html2text-1.3.2a-3.i586.rpm) and install them with (as root):

```
# rpm --nodeps -Uvh deb-1.10.27-3.i586.rpm
# rpm --nodeps -Uvh html2text-1.3.2a-3.i586.rpm

```

Also install rpmbuild:

```
# yum install rpm-build

```

Once this is done, you need to [download the alien-package](http://content.hccfl.edu/pollock/AUnix1/alien/alien_8.64.tar.gz) and unpack it, then installing it:

```
# wget http://content.hccfl.edu/pollock/AUnix1/alien/alien_8.64.tar.gz
# tar -zxvf alien_8.64.tar.gz
# cd alien/
# perl Makefile.PL
# make PREFIX=/usr
# make PREFIX=/usr install

```

Now [download the donkey debian package](http://ftp.no.debian.org/debian/pool/main/d/donkey/donkey_0.5-17_i386.deb), and convert it to rpm:

```
# wget http://ftp.no.debian.org/debian/pool/main/d/donkey/donkey_0.5-17_i386.deb
# alien --to-rpm donkey_0.5-17_i386.deb
# yum --nogpgcheck localinstall donkey-0.5-18.i386.rpm

```

And that’s it. :)

If you’re not bothered to go through the whole process just to get hold of the donkey-package, you can [find it here](http://jorge.ulver.no/files/donkey-0.5-18.i386.rpm).