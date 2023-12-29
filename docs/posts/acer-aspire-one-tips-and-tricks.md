---
title: Acer Aspire One Tips and Tricks
date: 2008-08-06
authors:
    - jorge
categories:
    - Linux
---
**Unfortunately, I no longer maintain this guide. Being that I myself now use Fedora for the Acer Aspire One, I don’t spend much time investigating how to tweak Linpus. All things you try based on this guide are at your own risk! I still recommend using http://www.aspireoneuser.com/forum/ for further questions about the Acer Aspire One. If you need help on IRC, you can find me on the channel #linuxhjelp on the IRC-network DALnet (irc.dal.net).**

I have, well had, a Powerbook G4 which was driving me nuts. Having been using Linux for a such a long time, I wasn’t quite able to adjust to how a Mac dealt with software – most of the stuff out there isn’t free. That was just one of the problems I had with a Mac. Making a long story short, I ended up with giving my Powerbook to my parents. I’m sure they’ll enjoy it way more than I do.

So, having gotten rid of that laptop, I suddenly found myself laptop-less, which, to be honest, left me feeling quite naked. I did find a solution though! Getting hold of an Acer Aspire One.

I’ve now ordered it, and it should be in my hands in about a week. Till then, I’ll just gather some tips and tricks concerning it and its system. The machine is finally in my hands. :) You can find the following tips and tricks here:

Please keep in mind that most changes done to the system will require you to have root-access. So unless specified otherwise, run all the commands as root. Whether you are root or not is indicated by the first character in your terminal. **$** signifies that you’re a regular user, **\#** is for when you are root.

Oh, and if you’re wondering about what the root password is, I believe it is the same as your regular user password. If you’d like to change it though, do the following:

```
1
2
3
4
5
```

```
$ sudo su -
# passwd root
New Unix password:
Retype new Unix password:
passwd: all authentication tokens updates successfully.

```



Before we proceed though, I need to emphasize that **every instruction here is done at your own risk**. There is no guarantee that any of this will work on your Acer Aspire One. Most people have reported these instructions to be a success, while others report failure. So, be cautious!

Changing to the default desktop
-------------------------------

The Acer Aspire One runs a Linux distribution called Linpus. This distribution comes with XFCE as the default windowmanager. Of course Acer has modified it a bit, which makes it look like this:

Word of caution though. Most websites will tell you to do the following:

su to root:

```
1
```

```
$ sudo su -

```



and open the file **/etc/xdg/xfce4-session/xfce4-session.rc**:

```
1
```

```
# mousepad /etc/xdg/xfce4-session/xfce4-session.rc

```



and find the line:

```
1
```

```
Client0_Command=xfdesktopnew
```



Change it to:

```
1
```

```
Client0_Command=xfdesktop-xfce
```



But the thing is, when you replace **xfdesktopnew** with **xfdesktop-xfce**, a script called **xfce-mcs-manager** won’t start up, and that is what some people find so “horrible” about the regular xfce-desktop. If we open up the file **xfdesktopnew** located in **/usr/bin/**, we find the following:

```
1
2
3
4
5
6
7
8
9
```

```
#!/bin/sh
if [ -f /usr/bin/xfce-mcs-manager.new ];then
sudo mv /usr/bin/xfce-mcs-manager.new /usr/bin/xfce-mcs-manager
fi
/usr/bin/xfdesktop2 & >/dev/null 2>&1
sleep 5
if [ -f /usr/bin/xfce-mcs-manager ];then
sudo  mv /usr/bin/xfce-mcs-manager /usr/bin/xfce-mcs-manager.new
fi

```



As you can se here the script **xfdesktopnew** starts the application **xfdesktop2**. What that script contains, I’ve no idea. Try opening the file for yourself, you’ll see what I’m talking about. Anyway, in order for this to work, change the line:

```
1
```

```
/usr/bin/xfdesktop2 & >/dev/null 2>&1
```



to:

```
1
```

```
/usr/bin/xfdesktop & >/dev/null 2>&1
```



Save it and close it. Next, open up the file **/usr/bin/xfdesktop**, and change line 6 to look like this:

```
1
```

```
/usr/bin/xfdesktop-xfce & >/dev/null 2>&1
```



Save, close, and reboot. You should have a fully working xfce desktop, with icons!

Now, if you’re like me, and would like to have the icons gone from the desktop, create a file named **xfdesktoprc** in **/home/user/.config/xfce4/desktop/** with the following in it:

```
1
2
3
4
5
```

```
[file-icons]
show-filesystem=false
show-home=false
show-trash=false
show-removable=true
```



Save and close, and reboot. The result? Something like my desktop:

Have fun. :)

Enabling the Right-click Menu
-----------------------------

Usually when you’re in xfce you can right-click on the desktop to bring forth a menu. This isn’t enabled as a standard function with the desktop that comes with the Acer Aspire One. To enable it, do the following.


```
1
```

```
$ xfce-setting-show

```



This will bring up the xfce settings manager. Select **Desktop**, **Behavior** and click the box named **Show desktop menu on right click**. Voila, that should be it. :)

Changing the keyboard layout to Norwegian
-----------------------------------------

This can be done in at least two ways. You either put **setxkbmap no** at the bottom of the **.bash\_profile**-file (located in your home-folder) like this:

```
1
```

```
 $ echo "setxkbmap no" >> bash_profile

```



The second method was downloading an RPM, but with the new Live Update-patch, this is no longer required.

Disabling autologin
-------------------

**Warning:** Take caution when commenting out lines, and inserting new ones. The lack of a single character may render your system useless.

Open the file named **/etc/rc.d/rc.S** and comment out the following line:

```
1
```

```
/usr/bin/xinit -- -br>/dev/null 2>&1 &
```



Like this:

```
1
```

```
#/usr/bin/xinit -- -br>/dev/null 2>&1 &
```



Below this line, insert the following:

```
1
```

```
/usr/sbin/gdm
```



And that’s that. The username is user and the password is whatever you set it to be during the installation.

Should you for some reason be unlucky enough to forget to add **/usr/sbin/gdm** to the file, but still commented out **/usr/bin/xinit — -br&gt;/dev/null 2&gt;&amp;1 &amp;**, and rebooted, do what David here did. Thanks David. :)

Installing Firefox 3
--------------------

If you want Firefox 3 to be installed properly (via **yum**), you should add the remi repository:

```
1
2
```

```
# wget http://rpms.famillecollet.com/remi-release-8.rpm
# rpm -Uvh remi-release-8.rpm

```



Edit the file **/etc/yum.repos.d/remi.repo**, and set **enabled** to **1** under **\[remi\]** and NOT **\[remi-test\]**.

Then we proceed with removing the old Firefox and installing the new one. Now, a regular **yum remove firefox** won’t work, as it’ll drag a buttload of dependencies with it. However, this will only remove Firefox, without its dependencies:

```
1
```

```
# rpm -e --nodeps firefox

```



Then install the new one:

```
1
```

```
# yum install firefox

```



That’s all there is to it. :)

Now, having said that, a friendly chap named Nacho Marin made me aware of a problem that had totally slipped my mind. It seems that there are several programs that are depending on some libraries that Firefox 2 has, and not Firefox 3. The Acer Aspire One e-mail client being one of them, and the RSS reader too. The missing libraries are libgtkembedmoz.so, libmozjs.so, libxpcom\_core.so and libxpcom.so.

A big thank you to Nacho for the heads up about the library-files.

**Update:** It seems that several people were complaining about how the e-mail program kept shutting down for no apparent reason. It seems that they need more than simply the library-files. I believe I’ve located the files, and that you now should be able to have both Firefox 3, and the email client working. Give me some feedback to let me know how the script turns out.

I (Jorge) have made a script to make this a bit easier to fix, so stay tuned.

**Update:** Script is finished, instructions are as follows:

Become root and open a file named **recover\_firefox\_libraries.sh**:

```
1
2
```

```
$ sudo su -
# mousepad recover_firefox_libraries.sh

```



Place the following in it:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
```

```
#!/bin/bash
#
## Script to recover Firefox 2 libraries
## Jorge Barrera Grandon 
## Version 2
#
#
## Usage: ./recover_firefox_libraries.sh

WGET=`which wget`
RPM_URL="http://jorge.fbarr.net/files/firefox-files.tar.gz"
MV=`which mv`
CHOWN=`which chown`
CHMOD=`which chmod`
RM=`which rm`
TAR=`which tar`
MKDIR=`which mkdir`
RMDIR=`which rmdir`

cd /root/
echo "## Getting hold of the Firefox-files.."
$WGET $RPM_URL

echo "## Unpacking the library-files.."
$TAR zxvf firefox-files.tar.gz

echo "## Moving library-files.."
$MV /usr/acer/bin/AME /usr/acer/bin/old.AME
$MV /root/firefox-files/AME /usr/acer/bin/AME
$MKDIR /usr/lib/firefox-files/
$MV /root/firefox-files/* /usr/lib/firefox-files/

echo "## Changing permissions and cleaning up.."
$RM /root/firefox-files.tar.gz
$RMDIR /root/firefox-files/
$CHOWN -R root.root /usr/lib/firefox-files/
$CHMOD -R 755 /usr/lib/firefox-files/

echo "## Done!"

```



What the script basically does is to get hold of the file **firefox-files.tar.gz** (so be online when running it), moves the libraryfiles, changes the permission, and removes the file and the unnecessary directories it creates. Make the file executable, then run it:

```
1
2
```

```
# chmod +x recover_firefox_libraries.sh
# ./recover_firefox_libraries.sh

```



Enjoy!

Installing other packages with yum
----------------------------------

If you want to install other packages with yum, a repository I can recommend is http://rpm.livna.org. You can add this repository by following these instructions. First, become root. You should be able to either do **sudo su –** and then entering the password for user, or just do **su –**, and entering the root-password (which I think is the same as for user):

```
1
2
```

```
 $ sudo su -
# yum -y install yum-priorities

```



Open and edit the file **/etc/yum/pluginconf.d/priorities.conf**. Change vim to whatever editor you prefer (**nano** is one of the easier ones):

```
1
```

```
# vim /etc/yum/pluginconf.d/priorities.conf

```



Make the contents of the file look like so:

```
1
2
3
```

```
[main]
enabled = 1
check_obsoletes = 1
```



Save and close the file. Now finally, install this package and you should be set:

```
1
```

```
# rpm -Uvh http://www.fedorafaq.org/f8/yum  http://rpm.livna.org/livna-release-8.rpm

```



Removing the search-bar
-----------------------

Removing the search-bar is very simple as well. Open the file **/usr/share/search-bar/start-search\_bar.sh** and comment out each line to make the content look like so:

```
1
2
3
4
5
6
7
8
9
10
11
```

```
#!/bin/sh
#resolution=`xrandr |grep *|awk '{print $1}'`
#resolution=`xrandr |grep current|awk -F"current" '{print $2}'|awk -F"," '{print $1}'|sed 's/ //g'`
#
#if [ $resolution = "1280x800" ];then
#   acer-search-desktop --x=650 --y=90 --width=490 --height=31
#elif [ $resolution = "1024x600" ];then
#   acer-search-desktop --x=510 --y=65 --width=490 --height=31
#else
#   acer-search-desktop
#fi

```



Enable Circular Scrolling
-------------------------

I doubt that this part is Linpus-specific, but after having had an Acer Aspire One for a few days, you’ll come to notice that the scrolling is damn annoying. The way to scroll as default is by dragging your finger along the right-hand side of the touchpad. And it isn’t even on the edge either, but a bit inside the edge. I personally found circular scrolling to be easier to use. Enable it like so.

Either press **Alt-F2** and type **gsynaptics** or open a terminal and type the same. Go to **Scrolling** and select **Enable Circular Scrolling**. Enjoy!

Adding your own Desktop-Icons
-----------------------------

You’re probably not going to use some of the the default applications listed on your Acer One-desktop. I know I’m not going to. But wouldn’t it be nice to be able to specify what programs you would like displayed on your desktop? Good thing it IS possible then. Have a look:

There you see that I’ve added two applications of my own – **VLC** and **audacious**. Here’s how you do it.

First off, you need to have the programs installed. :) In most cases you’ll already find that once you install the program, icons for it are present. Check **/usr/share/pixmaps/** for them. But should you need an icon for some program that hasn’t any at some point, it has to be 90×90, and be placed in that folder.

If you installed **VLC** through **yum**, like I did, then you’ll find the file **livna-vlc.desktop** in **/usr/share/applications/**. Now, for the sake of tidiness, I renamed the file to **vlc.desktop**:

```
1
```

```
# mv livna-vlc.desktop vlc.desktop

```



This file looks something like this:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
```

```
[Desktop Entry]
Version=1.0
Name=VLC media player
Name[fr]=Lecteur multimÃ©dia VLC
Comment=Read, capture, broadcast your multimedia streams
Comment[fr]=Lire, capturer, diffuser vos flux multimedia
Name[sv]=Mediaspelaren VLC
Comment[sv]=AllmÃ¤n uppspelare av film och musik
Name[ru]=ÐœÐµÐ´Ð¸Ð°Ð¿Ð»ÐµÐµÑ€ VLC
Comment[ru]=Ð£Ð½Ð¸Ð²ÐµÑ€Ñ&#129;Ð°Ð»ÑŒÐ½Ñ‹Ð¹ Ð¿Ñ€Ð¾Ð¸Ð³Ñ€Ñ‹Ð²Ð°Ñ‚ÐµÐ»ÑŒ Ð²Ð¸Ð´ÐµÐ¾ Ð¸ Ð°ÑƒÐ´Ð¸Ð¾
Exec=vlc
Icon=vlc.png

Terminal=false
Type=Application
Categories=AudioVideo;Player;
MimeType=video/dv;video/mpeg;video/x-mpeg;video/msvideo;video/quicktime;video/x-anim;video/x-avi;video/x-ms-asf;video/x-ms-wmv;video/x-msvideo;video/x-nsv;video/x-flc;video/x-fli;application/ogg;application/x-ogg;application/x-matroska;audio/x-mp3;audio/x-mpeg;audio/mpeg;audio/x-wav;audio/x-mpegurl;audio/x-scpls;audio/x-m4a;audio/x-ms-asf;audio/x-ms-asx;audio/x-ms-wax;application/vnd.rn-realmedia;audio/x-real-audio;audio/x-pn-realaudio;application/x-flac;audio/x-flac;application/x-shockwave-flash;misc/ultravox;audio/vnd.rn-realaudio;audio/x-pn-aiff;audio/x-pn-au;audio/x-pn-wav;audio/x-pn-windows-acm;image/vnd.rn-realpix;video/vnd.rn-realvideo;audio/x-pn-realaudio-plugin;application/x-extension-mp4;audio/mp4;video/mp4;video/mp4v-es;x-content/video-vcd;x-content/video-svcd;x-content/video-dvd;x-content/audio-cdda;x-content/audio-player;
X-Desktop-File-Install-Version=0.13
```



The next step is opening up the file **/home/user/.config/xfce4/desktop/group-app.xml**. Go through the file, you’ll see it’ll make sense. Now, I assume that you want **VLC** to be placed under the category **Fun**. Open the **group-app.xml-file**, and search for:

```
1
```

```
7
```



Somewhere in between there, add the following:

```
1
```

```
/usr/share/applications/vlc.desktop
```



Save and close the file, reboot, and your VLC-icon should be there. :) If you want your VLC-icon to be visible, like I did on my desktop, swap the **sequence=”6”** with a more appropriate number (like 1-3), just make sure that two objects in the file don’t have the same sequence number.

Or even easier – click and drag the icon you want displayed into one of the first three slots. :)

The right resolution with an external monitor
---------------------------------------------

If you’ve plugged in an external monitor, you’ve probably noticed that 1024×600 doesn’t look too good on a 17″. There is a remedy for this though, but you need to modify your **xorg.conf**-file.

First, take a backup of your current **xorg.conf**, in case something goes wrong:

```
1
```

```
# cp /etc/X11/xorg.conf /root/

```



Now open the file:

```
1
```

```
# mousepad /etc/X11/xorg.conf

```



Find the Screen-section – there should be a line there that says:

```
1
```

```
Virtual 1024 600
```



Change it to:

```
1
```

```
Virtual 2304 1024
```



Also add the resolutions “1280×1024” and “1024×768” to the Modes-line above. This will make sure that the virtual screen space is big enough to accomodate a 1280×1024 screen beside a 1024×600.

So that part of your **xorg.conf** should end up looking like this:

```
1
2
```

```
Modes "1280x1024" "1024x768" "1024x600" "800x600" "640x480"
Virtual 2304 1024
```



Save and close the file, and reboot to activate the virtual screen size.

Now, to be able to clone your desktop to the external monitor, press **Fn-F5**. If you however would like to use BOTH your Acer One Aspire, and your external monitor, open a file named **dual\_desktop.sh** and put the following in it:

```
1
2
3
```

```
#!/bin/bash
xrandr -s 1280x1024
xrandr --output LVDS --left-of VGA --auto

```



Save, close, and do a **chmod +x** on it:

```
1
```

```
$ chmod +x dual_desktop.sh

```



Press **Fn-F5** till both your Acer Aspire One and the external monitor are active. Then run the script:

```
1
```

```
$ ./dual_desktop.sh
```



Now when you run it, you should be able to drag applications between your Acer Aspire One, and your external monitor. :) Just change **-–left-of** to **-–right-of** if your Acer Aspire One is on that side. Change the resolution in the script to something else as well if you should need it. If you don’t feel like doing it though, here’s a script I’ve made that’s a little more advanced:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
```

```
#!/bin/bash
#
## Script to extend your Acer Aspire One (AA1) desktop
## Jorge Barrera Grandon 
#
#
## Press Fn-F5 on your AA1 till both monitors are 
## active then run this script. Usage is like so:
## ./dual_desktop.sh  
##
## Example: ./dual_desktop.sh 800x600 left

XRANDR=`which xrandr`

if [ "$1" = "1280x1024" ] || [ "$1" = "1024x768" ] || [ "$1" = "800x600" ] && [ "$2" = "left" ] || [ "$2" = "right" ]; then

    echo "Setting resolution to $1 and alignment to $2."
    $XRANDR -s $1
    $XRANDR --output LVDS --$2-of VGA --auto

else
    $XRANDR -s 1280x1024
    $XRANDR --output LVDS --left-of VGA --auto
    echo "No or invalid resolution given - setting resolution to 1280x1024 and alignment to left."

fi

```



If you want a resolution of 800×600 on your external monitor, and your Acer Aspire One is on your right-side, run the script like so:

```
1
```

```
$ ./dual_desktop.sh 800x600 right

```



Managing the (noisy) fan speed
------------------------------

At first my Acer Aspire One was working just fine. I never had any problems with it, nor the fan. But of late the thing has been making such a sound that it’s driving me nuts. Solution, solution, where art thou solution?! Well, there is a solution. :)

Download the scripts http://jorge.fbarr.net/files/acer\_ec.pl and http://jorge.fbarr.net/files/acerfand. Once downloaded, do a **chmod +x** on them, and place them in **/usr/local/bin/**. Add an entry in **/etc/rc.d/rc.local** to start acerfand, and create the configuration-file needed. So:

```
1
2
3
4
5
```

```
$ sudo su -
# wget http://jorge.ulver.no/files/{acer_ec.pl,acerfand}
# chmod +x acer*
# mv acer* /usr/local/bin/
# echo "/usr/local/bin/acerfand" >> /etc/rc.d/rc.local

```



Then create the file **acerfand.conf** in **/etc/** with the following options:

```
1
2
3
```

```
INTERVAL=5
FANOFF=55
FANAUTO=65
```



- INTERVAL is the polling interval in seconds.
- FANOFF is the temperature in Celsius at or below which to turn the fan off, if it’s currently on auto.
- FANAUTO is the temperature in Celsius at or above which to turn the fan to auto, if it’s currently off.

Righty, so when the default temperature reaches 55C, the fan works again. According to Intel, the Atom Chip could work at 99C. But I doubt anyone is willing to risk it. Heck, I know I don’t. Simply modify the values in **acerfand.conf** to your own liking. Enjoy!

USB Recovery Image
------------------

I’ve taken the liberty of uploading the image to a private server.

You can find the new and improved (not really ;) image at http://jorge.fbarr.net/files/aa1\_usb\_recovery\_image.gz.

Now, the instructions.

I had to use an external USB drive for this, as I don’t have a USB-pen that’s above 125MB, but it works just the same. Besides the Acer One Aspire, I’ve another machine with Fedora 9 on it. Once the external drive was plugged in, this is what **dmesg** came up with:

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
```

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
1
```

```
# zcat aa1_usb_recovery_image.gz > /dev/sdb

```



Notice that it’s **/dev/sdb** and **NOT /dev/sdb1**, as we’re overwriting the whole disk, and not just a partition of the disk.

That did the trick for me. Once the command was completed, remount the external drive and you’ll see that the new contents is there. Unmount it again, plug it into your Acer Aspire One, boot the machine, and press **F12** during boot to select the USB-pen/external harddrive as the device to boot. You should be on your way towards recovery. :)

If someone happens to know how to do this in Windows, please give me a shout so that I can add it to this post. :)

Improve read and write performance
----------------------------------

According to this post I found, concerning SSD, the following will greatly increase read and write performance on your SSD-disk (which happens to be the type of disk the Acer Aspire One has, the Linux-version anyway).

Simply do the following:

```
1
2
```

```
$ sudo su -
# mousepad /boot/grub/grub.conf

```



Now, add the **elevator=noop** option to the kernel-line, making this line:

```
1
```

```
kernel /boot/bzImage ro root=LABEL=linpus vga=0x311 splash=silent loglevel=1 console=tty1 quiet nolapic_timer
```



look like this:

```
1
```

```
kernel /boot/bzImage ro root=LABEL=linpus vga=0x311 splash=silent loglevel=1 console=tty1 quiet nolapic_timer elevator=noop
```



Save the file, close it, and reboot. Some have reported that there’s an increase in writespeeds going from 7.1MB/s to 9.1MB/s by adding this change. If it works or not, well, you can be the judge of that. :)

Another thing you can do to improve performance, is disabling Firefox’s cache. With IO (Input/Output) being the real bottleneck on the Acer Aspire One, disabling writing to the disk might be a good idea. When a page is loaded, Firefox will cache it into the hard disk so that it doesn’t have to be downloaded again for redisplaying. If you browse enough sites, you’ll experience that Firefox might freeze for a few seconds or so.


```
1
```

```
about:config
```



Search for **browser.cache.disk.enabled**, and if set to **True**, doubleclick it to set it to **False**.

Wallpaper for the Acer Aspire One
---------------------------------

I found the following wallpaper in this post, but I thought to post them here as well for easy access. The regular wallpaper on your AA1-desktop can become quite a drag with time. So why not spice it up a bit with some color?

The way to change the wallpaper is like so. Right-click on your desktop, select Settings then click on Desktop Settings. Next to File:, just enter the new path of your wallpaper. :)

Blue Red Green

Purple Royale Sepia

Troubleshooting
---------------

### VLC

The Fedoraproject-pages say the following:

The Fedora Project recently re-signed all of its packages with a new key. Background details regarding the key change are found here. This page exists to aide users in the transition to the newly signed content and further updates for Fedora 8 and Fedora 9.

I assume many (if not all) of you have by now experienced the following problem when installing VLC:

```
1
2
3
```

```
Error: Missing Dependency: libpulse.so.0(PULSE_0) is needed by package vlc
Error: Missing Dependency: libopendaap.so.0 is needed by package vlc-core
Error: Missing Dependency: libdvdnav.so.4 is needed by package vlc-core
```



The solution to this is to install the new key distributed by fedora. This is how you do it.

Download, verify and install this RPM-file:

```
1
2
```

```
$ sudo su -
# wget http://kojipkgs.fedoraproject.org/packages/fedora-release/8/6.transition/data/signed/4f2a6fd2/noarch/fedora-release-8-6.transition.noarch.rpm

```



Now verify that the package sha1sum matches **9a684ad36f4c1f49df7c569d5990d00f7da2cb9c**:

```
1
```

```
# sha1sum fedora-release-8-6.transition.noarch.rpm

```



Once that is verified, install the package through rpm:

```
1
```

```
# rpm -Uvh fedora-release-8-6.transition.noarch.rpm

```



Now import the new GPG-key like so:

```
1
```

```
# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-8-and-9

```



And that should be it. You should now be able to download and install VLC (among other things) as normal. :)

### ssh-agent

**ssh-agent** is quite a nifty thing to have running in the background. I thought that adding some lines of code to your **.bashrc** or **.bash\_profile** would do the trick. But apparently not. The code I added was this:

```
1
2
3
4
5
6
7
```

```
SSHAGENT="/usr/bin/ssh-agent"
SSHAGENTARGS="-s"

if [ -z "$SSH_AUTH_SOCK" -a -x "$SSHAGENT" ]; then
  eval `$SSHAGENT $SSHAGENTARGS`
    trap "kill $SSH_AGENT_PID" 0
fi

```



Adding it to either file didn’t work, because upon doing an **ssh-add**, I got the following error:

```
1
2
```

```
$ ssh-add
Could not open a connection to your authentication agent

```



And this happened even if **ssh-agent** was running:

```
1
2
```

```
$ ps aux | grep ssh-agent
user     2709  0.0  0.0   6332   604 ?        Ss   Aug28   0:00 /usr/bin/ssh-agent -s

```



The solution to this isn’t very intuitive. Open the file **/etc/xdg/xfce4/xinitrc**, and remove the comment from the following lines:

Line 72 – 77

```
1
2
3
4
5
6
```

```
#sshagent=`which ssh-agent`
kill_sshagent=0
#if test -z "$SSH_AGENT_PID" -a "$sshagent" -a "x$sshagent" != "xno"; then
#   eval `$sshagent -s`
#   kill_sshagent=1
#fi

```



Line 100 – 102

```
1
2
3
```

```
#       if test $kill_sshagent -eq 1; then
#           eval `$sshagent -k`
#       fi

```



When done, save and close the file, and reboot. ssh-add should now be working properly. :)

