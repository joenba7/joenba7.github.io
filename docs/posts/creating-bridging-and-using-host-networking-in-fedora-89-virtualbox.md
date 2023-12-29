---
title: Creating, bridging and using host networking in Fedora 8/9 (VirtualBox)
date: 2008-04-23
authors:
    - jorge
categories:
    - Linux
---
As I’ve mentioned in previous posts, I’m quite fond of VirtualBox, and thus we write some more about it! A default installation will give every virtual machine you create an ip of 10.0.2.15, but what if you’d like to enable your virtual machines to be able to interact with each other, each of them having their own ip? Here’s where bridging comes into play. As I’m using Fedora 8, this little section is for – that’s right – Fedora 8 (works in Fedora 9 as well). I’ll be assuming that your host-machine is getting its ip from a DHCP-server.

Add the following line to the bottom of */etc/sysconfig/network-scripts/ifcfg-eth0*:

```
BRIDGE=br0

```

Then create the file */etc/sysconfig/network-scripts/ifcfg-br0*, and add the following:

```
DEVICE=br0
TYPE=Bridge
BOOTPROTO=dhcp
ONBOOT=yes

```

Once you’ve done that, restart the network:

```
# service network restart

```

Now that we have that finished, you can create a virtual ethernet interface for VirtualBox like this:

```
# VBoxAddIF vbox0 USERNAME br0

```

Replace *USERNAME* with the username of the user you’ll be running VirtualBox as (the user michael would have put the username michael there).

Give all users write access to the device used by VirtualBox to communicate with the host network (*/dev/net/tun* in this case).

```
# chmod 0666 /dev/net/tun

```

Now, start VirtualBox and select your virtual machine, then go to *Settings*.

Then change *Attached to* to *Host Interface*, and type *vbox0* into the field *Interface Name*.

And that’s basically it! If you’d like to create another virtual interface for VirtualBox, just run the line above again, only replace *vbox0* with *vbox1*, *vbox2*, and so on. All depends on how many virtual machines you’re planning on running simultaneously.

Oh, if your virtual machine seems to stop at boot, when trying to get hold of the network information, it might be your firewall that’s causing the problem. So on the host-machine, disabling the firewall might do the trick:

```
# service iptables stop

```

Good luck!

