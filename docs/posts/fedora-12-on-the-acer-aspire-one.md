---
title: Fedora 12 on the Acer Aspire One
date: 2009-12-01
authors:
    - jorge
categories:
    - Linux
---
Another round of an Acer Aspire One tale, this time with Fedora 12 in play. Some of the newest additions to Fedora 12 are:

- Support for Bluetooth devices
- Improved NetworkManager
- Improved PackageKit
- Empathy, the new default IM-client (not in XFCE, you need to install it)
- Midori, a lightweight browser

And many other things. For more detailed information check out .

Let’s get down to business and install Fedora 12 on our Acer Aspire One, shall we?

#### Preparing your USB-pen

Here we are again, preparing our USB-pen for Fedora 12. Unfortunately, the only way you can get hold of the ISO is by downloading it with a torrent, so it might take a while if you don’t have the right ports opened!

What we need to do is to install the Live CD to a USB-pen/drive. [To do that download the torrent from here](http://torrent.fedoraproject.org/spins/Fedora-12-i686-Live-XFCE.torrent). With me having a stationary machine using Fedora 11, I’ll assume that you do as well. :D **su** to root and install the package **livecd-tools**:

```
# yum install livecd-tools

```

Once the package is installed, and the iso has been downloaded, plug in your USB-pen/drive. I will also assume that your USB-pen/drive has been given the name **sdb1**. You can check it with the command **dmesg**, just look at the bottom of output if it has detected it. It should look something like this:

```
sd 9:0:0:0: [sdb] 1974272 512-byte logical blocks: (1.01 GB/964 MiB)
sd 9:0:0:0: [sdb] Write Protect is off
sd 9:0:0:0: [sdb] Mode Sense: 00 00 00 00
sd 9:0:0:0: [sdb] Assuming drive cache: write through
sd 9:0:0:0: [sdb] Assuming drive cache: write through
 sdb: sdb1
sd 9:0:0:0: [sdb] Assuming drive cache: write through
sd 9:0:0:0: [sdb] Attached SCSI removable disk

```

If your USB-pen has previously had another operating system running on it, the **M**aster **B**oot **R**ecord (**MBR**) might already have some data on it that crashes with what we’re about to do. So to clear the **MBR** (excluding the partition table), you can do one of the two following things; Use the flag **–reset-mbr** when running **livecd-iso-to-disk** or using the **dd** command. I recommend using the first option, and if that fails, try the second one.

With your iso downloaded, execute the following command:

```
# livecd-iso-to-disk --reset-mbr /home/jorge/downloads/Fedora-12-i686-Live-XFCE/Fedora-12-i686-Live-XFCE.iso /dev/sdb1

```

**Fedora-12-i686-Live-XFCE.iso** being the Live CD iso we just downloaded. This command will copy the Live CD iso to your USB-pen, making that your bootable device.

Now, if the **MBR** is still causing problems (like the USB-pen not booting), try the following:

**Warning:** This command can render your system useless, so be **CAUTIOUS** and sure before you use it. Make sure that **/dev/sdb** is in fact your USB-pen, and not some other device. You have been warned!

```
# dd if=/dev/zero of=/dev/sdb bs=446 count=1

```

And run the **livecd-iso-to-disk**-command above again.

For more information about how to create and use a Live USB device, check out .

#### Installing Fedora 12

Now that the USB-pen is ready to go, reboot your Acer Aspire One, and at boot press **F12** to be able to select what boot device to boot from. Your device should pop up in that list. Log in as usual, and double-click the icon that says **Install to Hard Drive**. Follow the process as normal, however, when it gets time to partition the disk, there are a few changes we need to make.

We’ll be using the **ext4** filesystem on our Acer Aspire One.

We should be at the partitioning-screen now. Select **Use Entire Drive** (if you want to remove everything that is on the machine from before), and click the box that says **Review and modify partitioning layout**. Remember to click the box next to the device that says **sda**! If you select **sdb**, which should be your USB-pen, you’ll most likely wipe everything there. When done, click **Next**.

Now we should be in a new screen. We don’t want LVM on an SSD-disk, so we remove it.

Click on the line that says **vol\_MACHINENAME** and click **Delete**.

Now click on the line that says **/dev/sda2** and click **Edit**. Change the **File System Type** to **ext4** and then change the **Mount Point** to **/**. Make sure that you have checked the box **sda** (and **ONLY sda**) in **Allowable Drives** and selected **Fill to maximum allowable size**. When done, press **OK**, and then click **Next**. A question will pop up, telling you that you haven’t selected a swap partition, and if you still want to continue with this partitioning scheme. Select **Yes**, and then select **Write changes to disk**. When a question about a boot loader comes up, simply press **Next**.

When the formatting and installation is done, you can reboot your Acer, answer a few questions, and your system should be up and going.

##### Disabling SELinux

As usual, SELinux remains the same pain as before. So we remove it like we’ve done so many times before.

Once you’re in the system, press **Alt-F2**, type **terminal**, **su** to root, and edit the file **/etc/selinux/config** and set the variable **SELINUX** to **disabled**. Save and close the file.

#### Optimizing Fedora 12

I assume that by now you are actually using the Fedora 12 installation. Here’s how to improve the system performance a bit.

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
UUID=0dbcdea1-3f91-477a-b730-ea6033c45b5c /                       ext4    defaults,noatime,nodiratime        1 1
UUID=6f651dc4-3d50-4a28-b3d0-fc91b41f737c /boot                   ext4    defaults,noatime,nodiratime        1 2
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
kernel /vmlinuz-2.6.31.6-145.fc12.i686 ro root=UUID=0dbcdea1-3f91-477a-b730-ea6033c45b5c noiswmd LANG=en_US.UTF-8 SYSFONT=latarcyrheb-sun16 KEYBOARDTYPE=pc KEYTABLE=no rhgb quiet

```

Look like this:

```
kernel /vmlinuz-2.6.31.5-127.fc12.i686 ro root=UUID=0dbcdea1-3f91-477a-b730-ea6033c45b5c noiswmd LANG=en_US.UTF-8 SYSFONT=latarcyrheb-sun16 KEYBOARDTYPE=pc KEYTABLE=no rhgb quiet elevator=noop

```

Proceed with rebooting your machine, and everything should be working fine. :)

###### Removing journaling

Another thing you can do to increase the speed of your disk is disabling journaling of the **ext4** filesystem. I sure hope you have your USB-pen/drive in hand, because we’re going to need it. Reboot your machine, with your USB-thingie plugged in, and again press F12 at boot, selecting your USB-device as the boot device. When done booting to the Live CD, press Alt-F2 and type terminal, and su to root. Find out what the partition with Linux is named. If you’ve partitioned things according to this guide, it should be **/dev/sdb2**. It’s basically the partition that holds the root of your Linux.

What we first need to do is to edit the file **/etc/fstab** on your disk. Why? Some tools have yet to catch up. In particular, the version of **e2fsprogs** currently in Fedora 12 won’t read the UUIDs from such filesystems. And because the Fedora installer generates an **/etc/fstab** which identifies filesystems by UUID, if you just run the **tune2fs**-command we’ll be using, the system can’t find its root filesystem and so doesn’t get very far through the boot process.

So, in order to edit the **/etc/fstab**-file, we first mount up the partition **/dev/sda2**. So remember, Fedora 12 is now installed, but you boot on the LiveCD/LiveUSB, and have a terminal up and going. As root, execute the following commands:

```
# mkdir /mnt/disk
# mount /dev/sda2 /mnt/disk
# mount /dev/sda1 /mnt/disk/boot
# cd /mnt/disk/etc

```

Now open up the file named **fstab**. The two lines that currently look like this:

```
UUID=6f651dc4-3d50-4a28-b3d0-fc91b41f737c /                   ext4    defaults,noatime,nodiratime        1 1
UUID=0dbcdea1-3f91-477a-b730-ea6033c45b5c /boot                       ext4    defaults,noatime,nodiratime        1 2

```

Should look like this:

```
/dev/sda2 /                   ext4    defaults,noatime,nodiratime        1 1
/dev/sda1 /boot            ext4    defaults,noatime,nodiratime        1 2

```

We simply replaced the UUID-strings with the partitions **/dev/sda2** and **/dev/sda1**. Once you’ve edited the file, save it, and close it.

Now we need to update **/boot/grub/grub.conf**.

Execute this command:

```
# cd /mnt/disk/boot/grub/

```

And open up the file **grub.conf**. What used to look like this:

```
kernel /vmlinuz-2.6.31.6-145.fc12.i686 ro root=UUID=0dbcdea1-3f91-477a-b730-ea6033c45b5c noiswmd LANG=en_US.UTF-8 SYSFONT=latarcyrheb-sun16 KEYBOARDTYPE=pc KEYTABLE=no rhgb quiet elevator=noop

```

Should now look like this:

```
kernel /vmlinuz-2.6.31.6-145.fc12.i686 ro root=/dev/sda2 noiswmd LANG=en_US.UTF-8 SYSFONT=latarcyrheb-sun16 KEYBOARDTYPE=pc KEYTABLE=no rhgb quiet elevator=noop

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

Now should be the time to update all of your packages before proceeding. So when you are logged back into your system again, open up a terminal, su to root, and do a:

```
# yum update

```

Brace yourself, this might take a while. :)

#### Optimizing Firefox

There are quite a few ways to make your Firefox go faster, one of them being to disable caching completely. Try these out, and let me know how they work. In your Firefox, type **about:config** as your URL, and press enter. Search for these values, and change them to what I’ve written up here:

***Select all text when click on the URL bar***

In Windows and Mac, Firefox highlights all text when you click on the URL bar. In Linux, it does not select all the text. Instead, it places the cursor at the insertion point. Regardless which platform you are using, you can now tweak it to either select all or place cursor at insertion point.

- browser.urlbar.clickSelectsAll: **true**

##### Caching

When a page is loaded, Firefox will cache it into the hard disk so that it doesn’t need to be download again for redisplaying. The bigger the storage size you cater for Firefox, the more pages it can cache.

- browser.cache.disk.enable: **false**

**false** disabled caching completely, **true** enables it. If you want to modify the amount of cache used (instead of disabling it), have a look at **browser.cache.disk.capacity**.

##### Network

Config name: network.http.pipelining Default: False Modified value: **True**

Config name: network.http.proxy.pipelining Default: False Modified value: **True**

Config name: network.http.pipelining.maxrequests Default: 4 Modified value: **any value higher than 4, but not more than 8**

Config name: network.http.max-connections Default: 30 Modified value: **96**

Config name: network.http.max-connections-per-server Default: 15 Modified value: **32**

#### Enabling auto-login

First off, a huge thanks to the user **mjc** for this great tip which allows you to enable auto-login!

Edit the file **/etc/gdm/custom.conf** to make it look something like:

```
[daemon]
TimedLoginEnable=true
AutomaticLoginEnable=true
AutomaticLogin=jorge
TimedLogin=jorge
TimedLoginDelay=0

```

Replacing **jorge** with the username you want to automatically log in as. When done, save and exit the file.

Now you have to remove the keyring password. Yes, that does mean that your keys and passwords will be unprotected.

To change/remove the keyring password:

1. Open **Passwords and Encryption Keys** in **Accessories**
2. Go to the **Passwords**-tab
3. Right-click the **Passwords:login**-keyring and select **Change Password**.

Enter in your password in the top field and leave the new password fields (**Password** and **Confirm password**) empty to remove the keyring password. Click **Change**. Reboot your machine and you should log in automatically!

Thanks again **mjc**!

#### Ways to extend your battery life

There are several things you can do to extend the battery life on your Acer Aspire One.

- Turn the screen brightness down – your screen is the most power-intensive component in your laptop, and why turn the brightness all the way up if you already can see well enough?
- When not in use, turn of the wireless and bluetooth functions.
- If you have enough memory on your laptop, disable swapping (we’ve done it in this case). I would suggest putting in more memory, as swapping writes to virtual memory.
- Journaling-filesystem on a SSD-disk = baaad. More writing to disk means more power used. In this howto we’re using **ext4** without journaling, but just wanted to remind you. :)
- Disable all services that aren’t used. A lot of distributions come with say **httpd** enabled at start up. If you don’t need it, get rid of it.
- When you’re in no need of sound – mute it!

**Jason Farrel** has these following tips to apply as well:

- Use **powertop** to eliminate more power hogs.
- If using mplayer to play video, and you see nasty horizontal tearing, it’s because the fast overlay isn’t being used by default. Edit **~/.mplayer/config** and **gui.conf** to use **vo=xv:port=XX** where **XX** is the port returned from the **xvinfo** util for the overlay adaptor – this should be 79.
- To cut the bootup time almost in half (down to 25 seconds here), disable uneeded services. Here’s a bit of code to disable the services (run it as root): for s in atd auditd avahi-daemon bluetooth cups ip6tables \\ mdmonitor sendmail livesys livesys-late microcode\_ctl; \\ do echo “chkconfig $s off”; chkconfig $s off; done
- This one makes a big difference: don’t forget to disable Firefox’s disk cache or you’ll grind to death.

Thanks for the tips **Jason** (from the Fedora 10 post). :)

Now this next bit is VERY important, if you want your Acer Aspire One to perform as best as it can. Keep in mind that this applied to Fedora 10, and applies to Fedora 11 as well as Fedora 12:

*I installed FC10 like you described. It works fine. I was a bit surprised the AAO appeared rather slow compared to my EEE 1000H. So I ran **cat /proc/cpuinfo**. It appeared the CPU ran at 800 Mhz. I first looked at the BIOS, but the CPU ran at 1600 Mhz over there. Then I looked at services and found **cpuspeed** running. This is for scaling the CPU speed. After disabling this service the **cat /proc/cpuinfo** reported 1600 Mhz CPU speed and the system runs much smoother (and most probably consumes more power!).*

So start/stop the service:

```
# service cpuspeed [start|stop]

```

Throw in a:

```
# chkconfig cpuspeed [on|off]

```

To disable the service at boot-time.

Thanks to **Jaap** (from the Fedora 10 post) for the tip. :)

#### Minor troubleshooting

##### Make the sound work properly

When checking the initial volume-settings, you’ll notice that you can hear a little bit of sound, but not much, even if you’ve cranked up the volume to the max. Thing is, we need to specify a little something, just a line really, to make it work properly. Create a file named **/etc/modprobe.d/sound.conf** with the following in it:

```
options snd-hda-intel model=acer-aspire

```

Thanks to **Bruno Malone** (from the Fedora 10 post) for the tip. :)

In my case, I’ve removed **pulseaudio** because I tend to play movies over **NFS** quite a bit, and **pulseaudio** always makes the sound turn choppy. So after having executed a:

```
# yum remove pulseaudio

```

I add a volume-icon on my taskbar by right-clicking on it, selecting **Add New Items..** and adding the application **Mixer**.

Reboot, and your sound should be working perfectly fine, even after you’ve resumed from suspend. :)

##### Disabling Nouveau and using Intel

For me at least the driver Nouveau seems a bit choppy. So I decided to stick with Intel instead. Do the following to disable Nouveau and start using Intel:

```
# echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf

```

Proceed with opening up the file **/boot/grub/grub.conf** and add the following to the end of the **kernel**-line:

```
nouveau.modeset=0

```

Reboot and voila, you should be using Intel instead of Nouveau.

##### Using Fn-Arrow Up/Down to control the volume

If you come to notice that **Fn-Up**/**Down** doesn’t work for controlling the volume, it can easily be fixed. :) A program that’s needed, named **amixer**, isn’t installed. So as root, install it this way:

```
# yum -y install alsa-utils

```

That should make things peachy again. ;)

##### Why is everything so big?

This is easily fixed. Press the tiny Fedora-icon on your menu, and go to **Preferences**, **Appearance**, go to the tab **Fonts** and click the **Custom DPI Setting**, and type **80**. Done!

##### Double tap and scrolling doesn’t work on my Touchpad!

The new upstream Synaptics driver disables tapping and vertical edge scrolling by default. In order to fix this, first install the package **xorg-x11-drv-synaptics** like this:

```
# yum -y install xorg-x11-drv-synaptics

```

And place the following in a file called **/etc/hal/fdi/policy/10-synaptics.fdi**:

```

  
    

        synaptics
        1
       1

    
  


```

Save and close it, and reboot. Double tap should now be working.

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

**Note:** This section applies only to people who have a bad internet connection. This will increase the disk activity of the Acer Aspire One, so only use if you have a slow internet connection.

The presto plugin for yum adds support for downloading deltarpms and using them to generate new packages. If user enables this plugin, it will make a substantial dent in the amount of data having to be downloaded for updates. In other words, normally if you need to upgrade a package, yum will remove the complete package, and download the complete new package, even if a lot of the content is just the same. By enabling **yum-presto** it will only download the difference between those packages, making it faster and goes easier on the bandwidth by up to 80%, in some cases. Simply enable it by installing the **yum-presto** package:

```
# yum install yum-presto

```

And you’re set.

##### Installing Flash

As always, we need Flash for our browser. Thing is though, there’s an Adobe-repository we can use. If someone is bound to release a new version of Flash first, it’s them. So do the following as root:

```
# rpm -Uvh http://linuxdownload.adobe.com/adobe-release/adobe-release-i386-1.0-1.noarch.rpm
# yum -y install flash-plugin

```

That’s it. Restart your browser, and you should now have Flash enabled.

#### Final words

So, that was it for this round! This post will be constantly updated with new things I stumble upon, or suggestions from people posting comments. Anything you’d like to add to this post? Get in touch, I’ll look it over!

Can’t end this post without a screenshot of Fedora 12 on the Acer Aspire One, can we? What you see below is the IM-client Empathy, along with the browser Midori, a file opened within the terminal with vim and the Eye of Gnome-program showing a picture taken with Cheese.

[![fedora-12-xfce](http://jorge.fbarr.net/wp-content/uploads/2009/12/fedora-12-xfce-300x175.png)](http://jorge.fbarr.net/wp-content/uploads/2009/12/fedora-12-xfce.png)

As always, comments and feedback is always appreciated.