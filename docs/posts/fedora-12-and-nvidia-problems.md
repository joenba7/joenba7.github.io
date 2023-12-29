---
title: Fedora 12 and Nvidia-problems
date: 2009-11-26
authors:
    - jorge
categories:
    - Linux
---
**Update:** It seems that the packages **kmod-nvidia** are now in the normal section of the **rpmfusion**-repository. So try a:

```
yum install kmod-nvidia
```

Before attempting anything mentioned below.

This is probably just an initial thing, but, upon installing Fedora 12, if you have installed the **rpmfusion**-repo, you’ll probably come to notice that there are no Nvidia-packages available. The solution? Not much hassle really.

**Note:** If you don’t have the **rpmfusion**-repo installed, do it like so:

```
rpm -Uvh http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-stable.noarch.rpm http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-stable.noarch.rpm
```

Now, we proceed. First you need to disable the driver **nouveau** that comes as default with Fedora 12:

```
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf
```

Then open up your **grub.conf** and add the following to the end of the **kernel**-line:

```
nouveau.modeset=0 vga=0×318
```

Making it look something like:

```
kernel /vmlinuz-2.6.31.5-127.fc12.i686.PAE ro root=/dev/mapper/vg_cat-lv_root  LANG=en_US.UTF-8 SYSFONT=latarcyrheb-sun16 KEYBOARDTYPE=pc KEYTABLE=no rhgb quiet nouveau.modeset=0 vga=0×318
```

The thing is that the package isn’t yet in the normal sections of the **rpmfusion**-repository, but still under testing. So execute the following command to grab what you need:

```
yum --enablerepo=updates-testing install kmod-nvidia xorg-x11-drv-nvidia-libs.i686
```

And that should be it. :) Reboot your machine and you should have a working Fedora 12 with Nvidia-support.

