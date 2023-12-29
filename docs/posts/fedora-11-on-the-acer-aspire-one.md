---
title: Fedora 11 on the Acer Aspire One
date: 2009-06-11
authors:
    - jorge
categories:
    - Linux
---
So here we are again! Fedora 11 is finally out, with the new XFCE 4.6 and a lot of other goodies. I recently removed Fedora 10 from my Acer Aspire One, and installed Fedora 11. And let me tell you, it’s -awesome-. Goes a lot faster than Fedora 10, and pretty much everything works out of the box! Even the camera! Just a few minor tweaks, and pretty much everything is up and running. You can read more about what is new with Fedora 11 at .

Now, a lot of what you read here will pretty much be the same as what I wrote on the Fedora 10-article. The steps are similar, but some things need changing. So instead of having you guys jump back and forth between two posts, I’ll do it this way. Ready? Here we go.

#### Preparing your USB-pen

Here we are again, preparing our USB-pen for Fedora 11. Unfortunately, the only way you can get hold of the ISO is by downloading it with a torrent, so it might take a while if you don’t have the right ports opened!

What we need to do is to install the Live CD to a USB-pen/drive. [To do that download the torrent from here](http://spins.fedoraproject.org/torrents/Fedora-11-i686-Live-XFCE.torrent). With me having a stationary machine using Fedora 10, I’ll assume that you do as well. :D **su** to root and install the package **livecd-tools**:

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
# livecd-iso-to-disk /home/jorge/downloads/Fedora-11-i686-Live-XFCE/Fedora-11-i686-Live-XFCE.iso /dev/sdb1

```

**Fedora-11-i686-Live-XFCE.iso** being the Live CD iso we just downloaded. This command will copy the Live CD iso to your USB-pen, making that your bootable device.

#### Installing Fedora 11

Now that the USB-pen is ready to go, reboot your Acer Aspire One, and at boot press **F12** to be able to select what boot device to boot from. Your device should pop up in that list. Log in as usual, and double-click the icon that says **Install to Hard Drive**. Follow the process as normal, however, when it gets time to partition the disk, we’ll do this a little differently. :)

In my former howto we had to manually change from **ext3** to **ext2**, because of the journaling, and how it made the system even slower. Well let me tell you folks, **ext4** is working wonders. It’s going a lot quicker than everything did on the Fedora 10 system, you’ll see. :)

Anyway, enough rant from me, let’s continue.

We should be at the partitioning-screen now. Select **Use Entire Drive** (if you want to remove everything that is on the machine from before), and click the box that says **Review and modify partitioning layout**. When done, click **Next**.

Now we should be in a new screen. We’ll be modifying things a bit here. Thing is, I don’t want to use LVM, so I’m going with that. If you want to use LVM though, try checking out what I wrote on the Fedora 10-article.

Click on the line that says **VolGroup** and click **Delete**. Press **Delete** again to confirm it.

Now click on the line that says **/dev/sda2** and click **Edit**. Change the **Mount Point** to **/**, **File System Type** to **ext4**, and make sure that you have selected **Fill to maximum allowable size**. When done, press **OK**, and then click **Next**. A question will pop up, telling you that you haven’t selected a swap partition, and if you still want to continue with this partitioning scheme. Select **Yes**, and then select **Write changes to disk**. Now go read a book, watch some TV, or have a cup of joe. When the formatting and installation is done, you can reboot your Acer, answer a few questions, and your system should be up and going.

##### Disabling SELinux

SELinux hasn’t changed much with Fedora 11. Still the ol’ pain in the butt. So we remove it like we did last time.

Once you’re in the system, press **Alt-F2**, type **terminal**, **su** to root, and edit the file **/etc/selinux/config** and set the variable **SELINUX** to **disabled**. Save and close the file.

Last time we had a whole section about how we converted from an **ext3** system to **ext2**. Don’t need to this time, wohoo!

#### Optimizing Fedora 11

I assume that by now you are actually using the Fedora 11 installation. Here’s how to improve the system performance a bit.

##### Optimizing your disk

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
UUID=6f651dc4-3d50-4a28-b3d0-fc91b41f737c /boot                   ext3    defaults,noatime,nodiratime        1 2
UUID=0dbcdea1-3f91-477a-b730-ea6033c45b5c /                       ext4    defaults,noatime,nodiratime        1 1
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  defaults        0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
tmpfs      /var/log        tmpfs        defaults           0    0
tmpfs      /tmp            tmpfs        defaults           0    0
tmpfs      /var/tmp        tmpfs        defaults           0    0

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

/sbin/modprobe sdhci

```

Create a file named **blacklist\_msreader.conf** in **/etc/modprobe.d**, with the following in it:

```
blacklist jmb38x_ms

```

Open up the file **/boot/grub/grub.conf** and add **elevator=noop** to the kernel-line, making this:

```
kernel /vmlinuz-2.6.29.4-167.fc11.i586 ro root=UUID=0dbcdea1-3f91-477a-b730-ea6033c45b5c rhgb quiet

```

Look like this:

```
kernel /vmlinuz-2.6.29.4-167.fc11.i586 ro root=UUID=0dbcdea1-3f91-477a-b730-ea6033c45b5c rhgb quiet elevator=noop

```

Proceed with rebooting your machine, and everything should be working fine. :)

###### Removing journaling

Another thing you can do to increase the speed of your disk is disabling journaling of the **ext4** filesystem. I sure hope you have your USB-pen/drive in hand, because we’re going to need it. Reboot your machine, with your USB-thingie plugged in, and again press F12 at boot, selecting your USB-device as the boot device. When done booting to the Live CD, press Alt-F2 and type terminal, and su to root. Find out what the partition with Linux is named. If you’ve partitioned things according to this guide, it should be **/dev/sdb2**. It’s basically the partition that holds the root of your Linux.

What we first need to do is to edit the file **/etc/fstab** on your disk. Why? Some tools have yet to catch up. In particular, the version of **e2fsprogs** currently in Fedora 11 won’t read the UUIDs from such filesystems. And because the Fedora installer generates an **/etc/fstab** which identifies filesystems by UUID, if you just run the **tune2fs**-command we’ll be using, the system can’t find its root filesystem and so doesn’t get very far through the boot process.

So, in order to edit the **/etc/fstab**-file, we first mount up the partition **/dev/sda2**. So remember, Fedora 11 is now installed, but you boot on the LiveCD/LiveUSB, and have a terminal up and going. As root, execute the following commands:

```
# mkdir /mnt/disk
# mount /dev/sda2 /mnt/disk
# mount /dev/sda1 /mnt/disk/boot
# cd /mnt/disk/etc

```

Now open up the file named **fstab**. The two lines that currently look like this:

```
UUID=6f651dc4-3d50-4a28-b3d0-fc91b41f737c /boot                   ext3    defaults,noatime,nodiratime        1 2
UUID=0dbcdea1-3f91-477a-b730-ea6033c45b5c /                       ext4    defaults,noatime,nodiratime        1 1

```

Should look like this:

```
/dev/sda1 /boot                   ext3    defaults,noatime,nodiratime        1 2
/dev/sda2 /                       ext4    defaults,noatime,nodiratime        1 1

```

We simply replaced the UUID-strings with the partitions **/dev/sda2** and **/dev/sda1**. Once you’ve edited the file, save it, and close it.

Now we need to update **/boot/grub/grub.conf**.

Execute this command:

```
# cd /mnt/disk/boot/grub/

```

And open up the file **grub.conf**. What used to look like this:

```
kernel /vmlinuz-2.6.29.4-167.fc11.i586 ro root=UUID=0dbcdea1-3f91-477a-b730-ea6033c45b5c rhgb quiet elevator=noop

```

Should now look like this:

```
kernel /vmlinuz-2.6.29.4-167.fc11.i586 ro root=/dev/sda2 rhgb quiet elevator=noop

```

Save the file, close it, and unmount the partitions like this:

```
# cd ~/
# umount /mnt/disk/boot/
# umount /mnt/disk/

```

Now that they both are unmounted, all we have to do is run the **tune2fs**-command to disable journaling from the **ext4**-partition:

```
# tune2fs -O ^has_journal /dev/sda2

```

Reboot your machine, and you should now have an **ext4** filesystem with journaling disabled.

#### Optimizing Firefox

There are quite a few ways to make your Firefox go faster, one of them being to disable caching completely. Try these out, and let me know how they work. In your Firefox, type **about:config** as your URL, and press enter. Search for these values, and change them to what I’ve written up here:

***Select all text when click on the URL bar***

In Windows and Mac, Firefox highlights all text when you click on the URL bar. In Linux, it does not select all the text. Instead, it places the cursor at the insertion point. Regardless which platform you are using, you can now tweak it to either select all or place cursor at insertion point.

- browser.urlbar.clickSelectsAll: **true**

##### Caching

When a page is loaded, Firefox will cache it into the hard disk so that it doesn’t need to be download again for redisplaying. The bigger the storage size you cater for Firefox, the more pages it can cache.

- browser.cache.disk.enable: **false**

**false** disabled caching completely, **true** enables it. If you want to modify the amount of cache used though, have a look at **browser.cache.disk.capacity**.

##### Network

Config name: network.http.pipelining Default: False Modified value: **True**

Config name: network.http.proxy.pipelining Default: False Modified value: **True**

Config name: network.http.pipelining.maxrequests Default: 4 Modified value: **any value higher than 4, but not more than 8**

Config name: network.http.max-connections Default: 30 Modified value: **96**

Config name: network.http.max-connections-per-server Default: 15 Modified value: **32**

#### Enabling autologin

Enabling autologin isn’t too difficult, but it can be a bit tricky if you don’t know your way around. Word of warning though. There are a lot of things in the background that will break (like the permission to be able to suspend your machine) upon doing this. Haven’t investigated enough to give the proper permissions yet, but they will come with time.

If you’re still up for doing this, let’s execute these following commands. Remember that **username** is the user you want to automatically log in as:

```
$ su -
# cp /home/username/.bash_profile ~/username-bash_profile
# cp /etc/inittab ~/inittab.bak
# exit

```

At this point you should be logged in as the user you want to automatically log in as. Add the following lines to the file **/home/username/.bash\_profile**:

```
if [ -z "$DISPLAY" ] && [ $(tty) == /dev/tty1 ]; then
startxfce4
fi

```

Save and close the file.

Now that we’ve backed up things, and made that change, become **root** again, open up **/etc/inittab** with your favorite editor, and comment out the following line like so:

```

```

Save and close the file. Then proceed to opening up **/etc/event.d/tty1**, and comment out this line like so:

```
#exec /sbin/mingetty tty1

```

and adding the following below:

```
exec /sbin/mingetty --autologin username tty1

```

Remember to replace **username** with the user you want to login automatically as. Now that we’re done with that part, open up **/etc/sysconfig/desktop** and comment out the line there like so:

```
#PREFERRED=startxfce4

```

Adding the following just below it:

```
DISPLAYMANAGER="console"

```

Upon a reboot now you should be automatically logged in as the user you specified! A shutdown/reboot by pressing the **Logout** button though isn’t working at the moment, but do the following to get it working. Become **root** and open up the file **/etc/PolicyKit/PolicyKit.conf** and add make the whole file look like this:

```












```

That should make a shutdown and reboot work properly through the Logoff button. You will also notice now though that the sound isn’t working (permission-issues again). The fix to this is by adding the user to the groups **audio** and **video**. Open up **/etc/group** and make the lines with those groups look like so:

```
audio:x:63:username
video:x:39:username

```

Sound should now hopefully be working.

#### Ways to extend your battery life

There are several things you can do to extend the battery life on your Acer Aspire One.

- Turn the screen brightness down – your screen is the most power-intensive component in your laptop, and why turn the brightness all the way up if you already can see well enough?
- When not in use, turn of the wireless and bluetooth functions.
- If you have enough memory on your laptop, disable swapping (we’ve done it in this case). I would suggest putting in more memory, as swapping writes to virtual memory.
- Journaling-filesystem on a SSD-disk = baaad. More writing to disk means more power used. Switch to **ext2** instead.
- Disable all services that just aren’t used. A lot of distributions come with say **httpd** enabled at start up. If you don’t need it, get rid of it.
- When you’re in no need of sound – mute it!

**Jason Farrel** has these following tips to apply as well:

- Use **powertop** to eliminate more power hogs.
- If using mplayer to play video, and you see nasty horizontal tearing, it’s because the fast overlay isn’t being used by default. Edit **~/.mplayer/config** and **gui.conf** to use **vo=xv:port=XX** where **XX** is the port returned from the **xvinfo** util for the overlay adaptor – this should be 79.
- To cut the bootup time almost in half (down to 25 seconds here), disable uneeded services. Here’s a one-liner to disable them (run it as root): for s in atd auditd avahi-daemon bluetooth cups gpm ip6tables kerneloops mdmonitor nfslock portreserve rpcbind rpcgssd rpcidmapd sendmail setroubleshoot livesys livesys-late microcode\_ctl; do echo “chkconfig $s off”; chkconfig $s off; done
- This one makes a big difference: don’t forget to disable firefox’s disk cache or you’ll grind to death.

Thanks for the tips **Jason** (from the Fedora 10 post). :)

Now this next bit is VERY important, if you want your AAO to perform as best as it can. Keep in mind that this applied to Fedora 10, and applies to Fedora 11 as well:

*I installed FC10 like you described. It works fine. I was a bit surprised the AAO appeared rather slow compared to my EEE 1000H. So I ran **cat /proc/cpuinfo**. It appeared the CPU ran at 800 Mhz. I first looked at the BIOS, but the CPU ran at 1600 Mhz over there. Then I looked at services and found **cpuspeed** running. This is for scaling the CPU speed. After disabling this service the **cat /proc/cpuinfo** reported 1600 Mhz CPU speed and the system runs much smoother (and most probably consumes more power!).*

And he is completely right. **Jaap** (from the Fedora 10 post), thanks a million!

#### Minor troubleshooting

##### My Fn-Left/Right not only reduces/increases the brightness, but sends the machine into Suspend-mode. Why?

First off, a big thank you to **Lesley Mitchell** for having informed me of this issue. Quoting **Lesley**:

\_The brightness/suspend thing is a bug in xfce4-powermanager 0.8beta.

There’s a link to the release 0.8.0 version that fixes it here: [https://bugzilla.redhat.com/show\_bug.cgi?id=505414](https://bugzilla.redhat.com/show_bug.cgi?id=505414).

Or you can wait for it to turn up in the updates repository. \_

If you don’t want to wait for an update in the repository, do the following:

```
# wget http://kojipkgs.fedoraproject.org/packages/xfce4-power-manager/0.8.0/1.fc11/i586/xfce4-power-manager-0.8.0-1.fc11.i586.rpm
# yum --nogpgcheck localinstall xfce4-power-manager-0.8.0-1.fc11.i586.rpm

```

Reboot once it’s installed, and that should fix the issue.

##### Could not look up internet address for..

The complete errormessage is:

*Could not look up internet address for **NameOfYourMachine**. This will prevent Xfce from operating correctly. It may be possible to correct the problem by adding **NameOfYourMachine** to the file /etc/hosts on your system.*

The solution? Just like it says. If your machine name was **howl.fbarr.lan**, like mine is, you’d add the following:

```
127.0.0.1 howl.fbarr.lan howl

```

to **/etc/hosts**.

##### Make the sound work properly

When checking the initial volume-settings, you’ll notice that you can hear a little bit of sound, but not much, even if you’ve cranked up the volume to the max. Thing is, we need to specify a little something, just a line really, to make it work properly. Create a file named **/etc/modprobe.d/sound.conf** with the following in it:

```
options snd-hda-intel model=acer-aspire

```

If you were previously using **options snd-hda-intel model=acer**, change it to **options snd-hda-intel model=acer-aspire**. This should sort out your problem when plugging in a headphone, and the sound would still get out by the normal speaker at the same time. Thanks to **Bruno Malone** (from the Fedora 10 post) for the tip. :)

In my case, I’ve removed **pulseaudio** because I tend to play movies over **NFS** quite a bit, and **pulseaudio** always makes the sound turn choppy. So after having executed a **yum remove pulseaudio**, I add a volume-icon on my taskbar by right-clicking on it, selecting **Add New Items..** and adding the application **Mixer**.

Reboot, and your sound should be working perfectly fine, even after you’ve resumed from suspend. :)

##### Using Fn-Arrow Up/Down to control the volume

You’ll probably come to notice that **Fn-Left**/**Right** works perfectly fine for adjusting the screen brightness. Unfortunately, **Fn-Up**/**Down** doesn’t work for controlling the volume. But that is easily fixed. :) A program that’s needed, named **amixer**, isn’t installed. So as root, install it this way:

```
# yum -y install alsa-utils

```

That should make things peachy again. ;)

##### Why is everything so big?

This is easily fixed. Press the tiny Fedora-icon on your menu, and go to **Preferences**, **Appearance**, go to the tab **Fonts** and click the **Custom DPI Setting**, and type **80**. Done!

##### Double tap and scrolling doesn’t work on my Touchpad!

The new upstream Synaptics driver disables tapping and vertical edge scrolling by default. In order to fix this, place the following in a file called **/etc/hal/fdi/policy/10-synaptics.fdi**:

```

  
    

        synaptics
        1
       1

    
  


```

Save and close it, and reboot. Double tap should now be working.

If you want regular and circular scrolling enabled, you need to install the package **gsynaptics**, and running it from **Alt-F2**.

Enable the settings you want, and click OK. Now go to the XFCE start menu, **Preferences**, **Session and Startup**, and select the **Application Autostart-tab**. Add the application **/usr/bin/gsynaptics-init** to the startup list.

Settings should now be kept when rebooted.

##### Making suspend work when closing the lid

It seems that XFCE 4.6 comes with a native Suspend and Hibernate function, so this should work out of the box! Unfortunately, it doesn’t quite. Suspend works great, but I haven’t had success with Hibernate, yet. And thanks to **EasyTarget**, who commented on this post, I finally know why. Quoting **EasyTarget**:

*Jorge, I think the reason your Hibernate fails is that the Swap has been removed (for good reasons, I don’t have swap on my A110 either).. IIRC linux uses the swapfile for it’s hibernate functions, and not having a swapfile (or not having enough free space on the swap for your system ram+currently swapped data!) causes this to fail. Strangely this means you need a Huge swapfile to reliably hibernate your system.. I think it’s simply a no-go for SSD based systems at the moment. https://lists.linux-foundation.org/pipermail/linux-pm/2007-July/013971.html You might also be interested in.. http://wiki.debian.org/Hibernation/Hibernate\_Without\_Swap\_Partition*

Thanks for the information. :)

#### Other tips and tricks

Here are a few other minor tips and tricks.

##### Enabling the rpmfusion-repository

Easily done. su to root, and execute the following command:

```
# rpm -Uvh http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-stable.noarch.rpm http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-stable.noarch.rpm

```

And that’s it! Search for a package by executing:

```
# yum search 

```

##### Installing yum-presto

The presto plugin for yum adds support for downloading deltarpms and using them to generate new packages. If user enables this plugin, it will make a substantial dent in the amount of data having to be downloaded for updates. In other words, normally if you need to upgrade a package, yum will remove the complete package, and download the complete new package, even if a lot of the content is just the same. By enabling **yum-presto** it will only download the difference between those packages, making it faster and goes easier on the bandwidth by up to 80%, in some cases. Simply enable it by installing the **yum-presto** package:

```
# yum install yum-presto

```

And you’re set.

##### Installing Flash

As always, we need Flash for our browser. Thing is though, there’s an Adobe-repository we can use. If someone is bound to release a new version of Flash first, it’s them. So do the following as root:

```
# wget http://linuxdownload.adobe.com/adobe-release/adobe-release-i386-1.0-1.noarch.rpm
# yum --nogpgcheck localinstall adobe-release-i386-1.0-1.noarch.rpm
# yum -y install flash-plugin

```

That’s it. Restart your browser, and you should now have Flash enabled.

#### Final words

So, that was it! This post will be constantly updated with new things I stumble upon, or suggestions from people posting comments. Anything you’d like to add to this post? Get in touch, I’ll look it over!

And so, we end this post with another screenshot. This time, Fedora 11 with XFCE:

[![fedora-11-with-xfce](http://jorge.fbarr.net/wp-content/uploads/2009/06/fedora-11-with-xfce-300x175.png)](http://jorge.fbarr.net/wp-content/uploads/2009/06/fedora-11-with-xfce.png)

As always, comments and feedback is always appreciated.

Good luck, and enjoy!

