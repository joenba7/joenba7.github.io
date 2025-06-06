---
title: How to transfer images from iPhone to Fedora 42
date: 2025-06-06
authors:
    - jorge
categories:
    - Linux
tags:
    - iphone
    - fedora42
    - image-transfer
    - libimobiledevice
    - linux
    - rsync
---

I have an old iPhone that contains around 3-4000 images. For ages I have been wanting to transfer the images to my PC, and I did attempt it several times on Windows, with iTunes, and some other software. No such luck, as it either crashed or did not transfer all of the images. Needless to say the issue contributed to more of my hairs going grey. However! In Linux, it took me minutes!

<!-- more -->

First off, we need to install a couple of things:

```bash
dnf install ifuse libimobiledevice-utils gvfs-afc
```

Connect your iPhone to your PC with a USB-cable, and run as root:

```bash
ideviceinfo
```

This should return information about the device. If you get nothing, it's either issues with the cable, permissions or packages.

Now, create the mount point for your iPhone, say **~/iPhone**:

```bash
mkdir ~/iPhone
```

and now the magic. We mount the unit using ifuse and use rsync to copy over the files. There should be a **DCIM** folder under **~/iPhone** after having it mounted, that should contain your images and videos. As for your destination folder, in this case **~/Pictures/iPhone**, make sure that it exists first!

```bash
ifuse ~/iPhone
rsync -av ~/iPhone/DCIM ~/Pictures/iPhone/
```

When the sync has been completed, unmount the device:

```bash
fusermount -u ~/iphone
```

and that should be it! Hopefully you now have your pictures at **~/Pictures/iPhone/**!
