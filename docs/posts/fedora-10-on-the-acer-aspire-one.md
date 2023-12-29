---
title: Fedora 10 on the Acer Aspire One
date: 2008-11-10
authors:
    - jorge
categories:
    - Linux
---
**Update:** I have now reinstalled Fedora 10 on my Acer Aspire One, only this time I downloaded a spinoff, Fedora 10 XFCE Live CD, to see how it performed. And not a huge surprise, but it worked remarkably well, far better than Fedora 10 with GNOME. Not to mention that the battery time went from 1 hour and 50 minutes to 2 hours and 50 minutes. I’ve also modified this tutorial a bit, and I have followed these steps myself, so I know it works. But give me a shout anyway should you find that something is lacking.

Righty then. This is going to be one of those posts that probably is going to be a bit longer than usual (if you don’t include the Acer Aspire One-post).

I had settled down with Linpus on my Acer Aspire One, but it nagged me like mad that I had to just settle for it. I mean, Linpus is fine, but I want to have a system that’s up to date, I want to be able to remove packages as I see fit, without worrying that the hal-daemon breaks *shakes his fist at Linpus*. In any case, surfing the web yesterday, I came upon the post . So in fear of that post being lost at some point, I’m going to write something similar here, with my own hacks and modifications that I made. Here’s how I went about installing Fedora 10 on my Acer Aspire One.

### Preparing your USB-pen

For starters, by installing Fedora 10 XFCE Live CD, mostly everything works out of the box. That means webcam, SD-cards, wireless, you name it. How brilliant is that! But I’m getting a bit ahead of myself here. What we need to do first is to install the Live CD to a USB-pen/drive. [To do that download the torrent from here](http://spins.fedoraproject.org/torrents/Fedora-10-i686-Live-XFCE.torrent). With me having a stationary machine using Fedora 9, I’ll assume that you do as well. :D **su** to root and install the package **livecd-tools**:

```
# yum install livecd-tools

```

Once the package is installed, and the iso has been downloaded, plug in your USB-pen/drive. I will also assume that your USB-pen/drive has been given the name **sdb1**. You can check it with the command **dmesg**, just look at the bottom of output if it has detected it. It should look something like this:

```
sd 10:0:0:0: [sdb] Write Protect is off
sd 10:0:0:0: [sdb] Mode Sense: 00 00 00 00
sd 10:0:0:0: [sdb] Assuming drive cache: write through
sd 10:0:0:0: [sdb] Write Protect is off
sd 10:0:0:0: [sdb] Mode Sense: 00 00 00 00
sd 10:0:0:0: [sdb] Assuming drive cache: write through
sdb: sdb1
sd 10:0:0:0: [sdb] Attached SCSI disk

```

Now, with the USB-thingie plugged in, and your iso downloaded, execute the following command:

```
# livecd-iso-to-disk /home/jorge/downloads/Fedora-10-i686-Live-XFCE/F10-i686-Live-XFCE.iso /dev/sdb1

```

**F10-i686-Live-XFCE.iso** being the Live CD iso we just downloaded. This command will copy the Live CD iso to your USB-pen, making that your bootable device.

### Installing Fedora 10

Now that the USB-pen is ready to go, reboot your Acer Aspire One, and at boot press **F12** to be able to select what boot device to boot from. Your device should pop up in that list. Log in as usual, and double-click the icon that says **Install to Hard Drive**. Follow the process as normal, however, when it gets time to partition the disk, everything (and I mean everything) that is **ext3**, change it to **ext2**! Why? Because having a journaling system on a netbook that uses an SSD-disk is bad. You will experience that your system is pretty much crawling at the pace of a turtle. Anyway, when getting to the partition-section, remember to click on the box named **Review and Modify Partitioning Layout**, and press **Next**. In this next window, we want to remove the swap-partition. So click on the line that says **Swap** under **Type**, and click **Delete**. Now that the **Swap**-partition is gone, we still see that both **/** and **/boot/** are **ext**3. Click on each line, select **Edit**, and select the volume corresponding to it, then **Edit** again. There you can change from **ext3** to **ext2**. When done, click **Okay**. When pressing **Next** now, you’ll get a warning about not having a **Swap** partition. Ignore it and move on with the installation as usual.

### Disabling SELinux

Once your installation is all done, you need to reboot for changes to take effect. Answer some questions, create a user, and you’re done! ..for now. Still have a lot of things to do. The first thing that comes to mind is to make your root-filesystem to be **ext2**. But didn’t we specify that when we partitioned? We sure did. Why the root partition STILL is **ext3** is beyond me, but we need to fix that. The user **Turnip** mentioned that he didn’t really feel like having LVM, so he just deleted the big partition that held it, and recreated an unvarnished ext2 partition in it’s place. When he rebooted to change what he believed was **ext3**, it was in fact still **ext2**. I have to test this myself to be certain (I sure wish I had an Acer Aspire One for testing purposes).

Before we fix the whole LVM/ext3-problem though, we need to disable **SELinux**. **SELinux** is such a pain in the butt it’s unbelieveable. Press **Alt-F2**, type **terminal**, **su** to root, and edit the file **/etc/selinux/config** and set the variable **SELINUX** to **disabled**. Save and close the file.

### Converting the filesystem from ext3 to ext2

I sure hope you have your USB-pen/drive in hand, because we’re going to need it. Reboot your machine, with your USB-thingie plugged in, and again press **F12** at boot, selecting your USB-device as the boot device. When done booting to the Live CD, press **Alt-F2** and type **terminal**, and **su** to root.

What we need to do next is to convert the root-partition of our system to **ext2** (as it is currently **ext3**). The root filesystem is on an LVM-partition, so the way to change it isn’t exactly your usual way of doing it. Run the command **vgscan**, and it’ll look something like this:

```
# vgscan
Reading all physical volumes.  This may take a while...
Found volume group "VolGroup00" using metadata type lvm2

```

You can now find your root partition in **/dev/VolGroup00/LogVol00**. To convert this to ext2, run the following command:

```
# tune2fs -O ^has_journal /dev/VolGroup00/LogVol00
# e2fsck /dev/VolGroup00/LogVol00

```

When this is done, your system should be **ext2**. To test this, you can do the following things:

```
# mkdir /mnt/disk
# mount -t ext3 /dev/VolGroup00/LogVol00 /mnt/disk

```

This last command should fail, as we’re not mounting an **ext3** partition. Try it with **ext2**, it should now work.

Next step is to update an important file, and making a new **initrd**.

### Updating /etc/fstab and making a new initrd

Now that our filesystem is updated, we need to change the entries in **/mnt/disk/etc/fstab** to read and specify the root partition as **ext2**, and not **ext3**. So open up the file **/mnt/disk/etc/fstab**, and replace **ext3** with **ext2**. When done, save and close the file. Remember that you need to do these changes as root!

We need to do some changes to the boot partition as well, so mount it up, and other things as well (thanks **skug67**!):

```
# mount /dev/sda1 /mnt/disk/boot/
# mount -o bind /sys /mnt/disk/sys
# mount -o bind /proc /mnt/disk/proc

```

What we will do now is to change our root-enviroment so that we’re working directly inside the mounted partitions. You can do that by running this command:

```
# chroot /mnt/disk/ /bin/bash

```

We’re now in the new enviroment. It’s time to create the new **initrd**. Now, the image-versions shown below does not necessarily have to be what you have. The iso you downloaded from the Fedora torrent might be newer than what I’m writing here, so just do an **ls /boot/** to see what your image-file looks like. We proceed:

```
# mv initrd-2.6.27.5-117.fc10.i686.img initrd-2.6.27.5-117.fc10.i686.img.ext3
# mkinitrd initrd-`uname -r`.img `uname -r`

```

When this is done, you’ve created a new **initrd**. Follow up with exiting the enviroment, unmounting the partitions and rebooting:

```
# exit
# cd /root/
# umount /mnt/disk/boot/
# umount /mnt/disk/sys/
# umount /mnt/disk/proc/
# umount /mnt/disk/
# reboot

```

With **SELinux** gone, **ext3** converted to **ext2**, and a new **initrd** created, you should be able to boot into your Fedora 10 on the Acer Aspire One. :)

And now, optimizing the system.

### Optimizing Fedora 10

I assume that by now you are actually using the Fedora 10 installation. Here’s how to improve the system performance a bit.

Quoting the [Ubuntu wikipages](https://help.ubuntu.com/community/AspireOne):

*Frequent writes to the SSD will cause failure eventually. We can reduce the number of writes to the SSD by moving our logs to a temporary filesystem in RAM that gets destroyed at ever reboot. Now this means your logs will not be persistent across reboots making debugging difficult in some cases. This step is optional of course, so if you need the logs for an extended period of time do not follow these steps.*

Let’s assume that you want to do this. su to root, and open up the file **/etc/fstab**, and place these following lines there:

```
tmpfs      /var/log        tmpfs        defaults           0    0
tmpfs      /tmp            tmpfs        defaults           0    0
tmpfs      /var/tmp        tmpfs        defaults           0    0

```

Also, modify the first line in **/etc/fstab** to include the options noatime and nodiratime. When done, your **/etc/fstab** should look something like this:

```
/dev/VolGroup00/LogVol00 /                       ext2    defaults,noatime,nodiratime        1 1
UUID=43882dc4-109b-40c9-b3eb-6e2c7ebd2758 /boot                   ext2    defaults        1 2
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
tmpfs                   /var/log            tmpfs   defaults        0 0
tmpfs                   /var/tmp                tmpfs   defaults        0 0
tmpfs                   /tmp                tmpfs   defaults        0 0

```

Save the file, and close it. The next thing we can do is to place the following into your **/etc/rc.d/rc.local**-file:

```
# Economize the SSD
sysctl -w vm.swappiness=1               # Strongly discourage swapping
sysctl -w vm.vfs_cache_pressure=50      # Don't shrink the inode cache aggressively

# As in the rc.last.ctrl of Linpus
echo ondemand > /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
echo ondemand > /sys/devices/system/cpu/cpu1/cpufreq/scaling_governor
cat /sys/devices/system/cpu/cpu0/cpufreq/ondemand/sampling_rate_max > /sys/devices/system/cpu/cpu0/cpufreq/ondemand/sampling_rate

echo 1500 > /proc/sys/vm/dirty_writeback_centisecs
echo 20 > /proc/sys/vm/dirty_ratio
echo 10 > /proc/sys/vm/dirty_background_ratio

echo 1 > /sys/devices/system/cpu/sched_smt_power_savings
echo 10 > /sys/module/snd_hda_intel/parameters/power_save
echo 5 > /proc/sys/vm/laptop_mode

#Decrease power usage of USB while idle
[ -w /sys/bus/usb/devices/1-5/power/level ] && echo auto > /sys/bus/usb/devices/1-5/power/level
[ -w /sys/bus/usb/devices/5-5/power/level ] && echo auto > /sys/bus/usb/devices/5-5/power/level

/sbin/setpci -d 197b:2381 AE=47
/sbin/modprobe pciehp
/sbin/modprobe sdhci

```

Create a file named blacklist\_msreader in /etc/modprobe.d, with the following in it:

```
blacklist jmb38x_ms

```

Open up the file **/boot/grub/grub.conf** and add **elevator=noop** to the kernel-line, making this:

```
kernel /vmlinuz-2.6.27.5-117.fc10.i686 ro root=/dev/VolGroup00/LogVol00 rhgb quiet

```

Look like this:

```
kernel /vmlinuz-2.6.27.5-117.fc10.i686 ro root=/dev/VolGroup00/LogVol00 rhgb quiet elevator=noop

```

Proceed with rebooting your machine, and everything should be working fine. :)

### Ways to extend your battery life

There are several things you can do to extend the battery life on your Acer Aspire One.

- Turn the screen brightness down – your screen is the most power-intensive component in your laptop, and why turn the brightness all the way up if you already can see well enough?
- When not in use, turn of the wireless and bluetooth functions.
- If you have enough memory on your laptop, disable swapping. I would suggest putting in more memory, as swapping writes to virtual memory.
- Journaling-filesystem on a SSD-disk = baaad. More writing to disk means more power used. Switch to **ext2** instead.
- Disable all services that just aren’t used. A lot of distributions come with say **httpd** enabled at start up. If you don’t need it, get rid of it.
- When you’re in no need of sound – mute it!

**Jason Farrel** has these following tips to apply as well:

- Not exactly a performance tweak, but add **vga=0×315** to the end of the kernel-line in **grub.conf** in order to see the fancy new plymouth solar bootup screen. Kernel mode setting isn’t yet supported for the intel gma display, so this vesa mode set workaround is needed.
- Use **powertop** to eliminate more power hogs.
- If using mplayer to play video, and you see nasty horizontal tearing, it’s because the fast overlay isn’t being used by default. Edit **~/.mplayer/config** and **gui.conf** to use **vo=xv:port=XX** where **XX** is the port returned from the **xvinfo** util for the overlay adaptor – this should be 79.
- To cut the bootup time almost in half (down to 25 seconds here), disable uneeded services. Here’s a one-liner to disable them (run it as root): for s in atd auditd avahi-daemon bluetooth cups gpm ip6tables kerneloops mdmonitor nfslock portreserve rpcbind rpcgssd rpcidmapd sendmail setroubleshoot livesys livesys-late microcode\_ctl; do echo “chkconfig $s off”; chkconfig $s off; done
  
  
  - This one makes a big difference: don’t forget to disable firefox’s disk cache or you’ll grind to death.

Thanks for the tips **Jason**. :)


*I installed FC10 like you described. It works fine. I was a bit surprised the AAO appeared rather slow compared to my EEE 1000H. So I ran **cat /proc/cpuinfo**. It appeared the CPU ran at 800 Mhz. I first looked at the BIOS, but the CPU ran at 1600 Mhz over there. Then I looked at services and found **cpuspeed** running. This is for scaling the CPU speed. After disabling this service the **cat /proc/cpuinfo** reported 1600 Mhz CPU speed and the system runs much smoother (and most probably consumes more power!).*

And he is completely right. **Jaap**, thanks a million!

### Minor troubleshooting

Make the sound work properly
----------------------------

When checking the initial volume-settings, you’ll notice that you can hear a little bit of sound, but not much, even if you’ve cranked up the volume to the max. Thing is, we need to specify a little something, just a line really, to make it work properly. Create a file named **/etc/modprobe.d/sound** with the following in it:

```
options snd-hda-intel model=acer-aspire

```

If you were previously using **options snd-hda-intel model=acer**, change it to **options snd-hda-intel model=acer-aspire**. This should sort out your problem when plugging in a headphone, and the sound would still get out by the normal speaker at the same time. Thanks to **Bruno Malone** for the tip. :)

Reboot, and your sound should be working perfectly fine, even after you’ve resumed from suspend. :)

### Using Fn-Arrow Up/Down to control the volume

You’ll probably come to notice that **Fn-Left**/**Right** works perfectly fine for adjusting the screen brightness. Unfortunately, **Fn-Up**/**Down** doesn’t work for controlling the volume. But that is easily fixed. :) A program that’s needed, named **aumix**, isn’t installed. So as root, install it this way:

```
# yum -y install aumix

```

That should make things peachy again. ;)

### Making the Wifi LED work

In order for you to have a functional Wifi LED working, you need to have **madwifi** installed. Upon installing **madwifi**, it will automatically blacklist the atheros-driver for your wireless card. Don’t get me wrong, your wireless will still work (and rumor has it that **madwifi** is faster). Another reason that you might want to use **madwifi** instead of the one included in the kernel is that there seems to be a weird bug where the wireless flakes to the point where it won’t work until you do a full power down (pulling battery and discharging the caps). To be able to install **madwifi** you need to have the **rpmfusion**-repository enabled. You can enable it by doing so:

```
# rpm -Uvh http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-stable.noarch.rpm http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-stable.noarch.rpm

```

Once that is done, you can continue by installing **madwifi** like this:

```
# yum -y install madwifi

```

Once it is installed, add the following to the bottom of the file **/etc/sysctl.conf**:

```
dev.wifi0.ledpin = 3
dev.wifi0.softled = 1

```

Now reboot your machine, and your Wifi LED on the front will now do the association blink, as well as blink based on wireless traffic.

### Making suspend work when closing the lid

When closing the lid on your Acer Aspire One, you expect it to suspend, right? This behaviour does not come as default with the XFCE desktop. You can make it work by doing the following.

First you have to have **acpid** installed. Execute:

```
# yum install acpid

```

to install it. Once it is installed, create a file named **lid.conf** in **/etc/acpi/events/** and place the following in it:

```
event=button/lid.*
action=/usr/sbin/pm-suspend

```

This will now suspend your machine when closing the lid.

If your wireless networking isn’t working after resuming from suspend, and you are using the **madwifi** driver instead of the native kernel provided driver, you can prevent that problem by placing the following content in a file named **/usr/lib/pm-utils/sleep.d/06acerwifi**:

```
#!/bin/sh

. "${PM_FUNCTIONS}"

unload_madwifi()
{
/usr/bin/madwifi-unload > /dev/null
}

load_madwifi()
{
/sbin/modprobe ath_pci > /dev/null
}

case "$1" in
hibernate|suspend)
unload_madwifi
;;
thaw|resume)
load_madwifi
;;
*) exit $NA
;;
esac

```

When done, change the permissions:

```
# chmod 755 06acerwifi

```

Reboot, and it should suspend when closing the lid. :) The only problem with this is that NetworkManager won’t automatically connect to your previous wireless network, so you’ll have to click and select it. I’ll see what I can find out about it. :)

Thanks to **derge** from the [Fedoraforums](http://www.fedoraforums.org) for the tip about the resume-sound from suspend issue.

That’s pretty much what I have, so far. But I can’t end this post without an image. :) This is how my Fedora 10 XFCE desktop looks on the Acer Aspire One.

[![](http://jorge.fbarr.net/wp-content/uploads/2008/12/fedora_10_xfce_on_the_acer_aspire_one-300x175.png)](http://jorge.ulver.no/wp-content/uploads/2008/12/fedora_10_xfce_on_the_acer_aspire_one.png)

Good luck!

