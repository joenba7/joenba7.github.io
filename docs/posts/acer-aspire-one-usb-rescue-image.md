---
title: Acer Aspire One USB rescue image
date: 2010-09-14
authors:
    - jorge
categories:
    - Linux
---
A lot of people have been asking about where to find a rescue USB image for the Acer Aspire One. A long time ago someone with the nick **gouki** volunteered to host the file on a private server, but unfortunately, this is not so anymore.

I have placed the file on a new server, so you can now find it at [http://jorge.fbarr.net/files/aa1\_usb\_recovery\_image.gz](http://jorge.fbarr.net/files/aa1_usb_recovery_image.gz). Right on the link and select **Save link as** to download it.

As for instructions on how to use it, I’ll repost a snippet of what I’ve written previously in the article [Acer Aspire One Tips and Tricks](http://jorge.fbarr.net/2008/08/06/acer-aspire-one-tips-and-tricks/): \_ I had to use an external USB drive for this, as I don’t have a USB-pen that’s above 125MB, but it works just the same. Besides the Acer One Aspire, I’ve another machine with Fedora 9 on it. Once the external drive was plugged in, this is what **dmesg** came up with:

```
usb 2-2.1: New USB device found, idVendor=1058, idProduct=0702
usb 2-2.1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
usb 2-2.1: Product: External HDD
usb 2-2.1: Manufacturer: Western Digital
usb-storage: device found at 14
usb-storage: waiting for device to settle before scanning
usb-storage: device scan complete
scsi 16:0:0:0: Direct-Access     WD       1200BEVExternal  1.02 PQ: 0 ANSI: 0
sd 16:0:0:0: [sdb] Write Protect is off
sd 16:0:0:0: [sdb] Mode Sense: 00 00 00 00
sd 16:0:0:0: [sdb] Assuming drive cache: write through
sd 16:0:0:0: [sdb] Write Protect is off
sd 16:0:0:0: [sdb] Mode Sense: 00 00 00 00
sd 16:0:0:0: [sdb] Assuming drive cache: write through
sdb: sdb1
sd 16:0:0:0: [sdb] Attached SCSI disk
sd 16:0:0:0: Attached scsi generic sg2 type 0
```

So now we know that the drive has been assigned **/dev/sdb**. Let’s make the recovery usb drive then.


```
zcat aa1_usb_recovery_image.gz > /dev/sdb
```

Notice that it’s **/dev/sdb** and NOT **/dev/sdb1**, as we’re overwriting the whole disk, and not just a partition of the disk.

That did the trick for me. Once the command was completed, remount the external drive and you’ll see that the new contents is there. Unmount it again, plug it into your Acer Aspire One, boot the machine, and press **F12** during boot to select the USB-pen/external harddrive as the device to boot. You should be on your way towards recovery. :)

If someone happens to know how to do this in Windows, please give me a shout so that I can add it to this post. :)

A big thanks to **Mithrandir** for providing the file. :)