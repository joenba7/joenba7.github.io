---
title: Introduction to lsof
date: 2013-09-19
authors:
    - jorge
categories:
    - Linux
tags:
    - connections
    - file
    - filesystem
    - linux
    - list
    - lsof
    - network
    - process
    - tcp
    - tutorial
    - udp
---
Today I’m going to introduce you to the exciting world of **lsof**.

As you will see, it’s a nifty little tool that has various uses, and it has so many switches that you need both a **–** and a **+** to use all the options available! As the description of the command says in its **man**-page:

> lsof – list open files

So, let’s get started.

Network
-------

### List network connections

```
$ lsof -i
COMMAND    PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
ssh        868 jorge    3u  IPv4  12710      0t0  TCP j-laptop.fbarr.lan:55908->login.redpill-linpro.com:ssh (ESTABLISHED)
chrome     967 jorge  114u  IPv4 372589      0t0  TCP j-laptop.fbarr.lan:44756->edge-star-ecmp-04-ams2.facebook.com:https (ESTABLISHED)
...
```

### List TCP/UDP connections

```
$ lsof -i TCP
﻿﻿COMMAND    PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
chrome     967 jorge  203u  IPv4 403952      0t0  TCP j-laptop.fbarr.lan:35942->173.192.82.194-static.reverse.softlayer.com:http (ESTABLISHED)
ssh        868 jorge    3u  IPv4  12710      0t0  TCP j-laptop.fbarr.lan:55908->login.acme.com:ssh (ESTABLISHED)
```

Simply replace **TCP** with **UDP** to see **UDP** connections.

### List connections related to a port

```
$ lsof -i :22
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
ssh     1742 jorge    3u  IPv4 448777      0t0  TCP j-laptop.fbarr.lan:36484->j-desktop.fbarr.lan:22 (ESTABLISHED)
ssh     5409 jorge    3u  IPv4  73369      0t0  TCP j-laptop.fbarr.lan:45986->j-server.fbarr.lan:22 (ESTABLISHED)
```

### List connections related to a host

```
$ lsof -i @192.168.1.1
COMMAND PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
chrome  967 jorge  143u  IPv4 526978      0t0  TCP j-laptop.fbarr.lan:38571->calcifer.fbarr.lan:https (ESTABLISHED)
```

..and port:

```
$ lsof -i @192.168.1.1:443
chrome  967 jorge  143u  IPv4 526978      0t0  TCP j-laptop.fbarr.lan:38571->calcifer.fbarr.lan:https (ESTABLISHED)
```

It’s also possible to provide a port-range:

```
$ lsof -i :443-1000
COMMAND   PID  USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
chrome   4598 jorge   64u  IPv4   68058      0t0  TCP j-laptop.fbarr.lan:35515->do-20.lastpass.com:https (ESTABLISHED)
chrome   4598 jorge   98u  IPv6 5251212      0t0  TCP j-laptop.fbarr.lan:43729->lb-in-x5e.1e100.net:https (ESTABLISHED)
```

### List connections with status LISTEN

```
$ sudo lsof -i | grep LISTEN
sshd      11903  root    3u  IPv4 549183      0t0  TCP *:ssh (LISTEN)
sshd      11903  root    4u  IPv6 549185      0t0  TCP *:ssh (LISTEN)
```

Replace **LISTEN** with **ESTABLISHED**, and various other options, to grab what you’re looking for.

### List IPv4/IPv6 connections

```
$ lsof -i 4
COMMAND   PID  USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
chrome   4598 jorge   64u  IPv4   68058      0t0  TCP j-desktop.fbarr.lan:35515->do-20.lastpass.com:https (ESTABLISHED)
mutt    14654 jorge    3u  IPv4   41678      0t0  TCP j-desktop.fbarr.lan:57323->87.144.18.114:imaps (ESTABLISHED)

$ lsof -i 6
COMMAND   PID  USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
chrome   4598 jorge  127u  IPv6 5278504      0t0  TCP j-desktop.fbarr.lan:42348->lb-in-x5d.1e100.net:https (ESTABLISHED)
chrome   4598 jorge  182u  IPv6  132082      0t0  TCP j-desktop.fbarr.lan:59281->arn02s06-in-x0e.1e100.net:https (ESTABLISHED)
```

Users, processes and files
--------------------------

**lsof** not only is useful for network-related tasks, but works excellent for users, processes and files.

### List open files of user

```
$ lsof -u jorge
...
COMMAND     PID  USER   FD      TYPE     DEVICE SIZE/OFF    NODE NAME
lsof      26305 jorge  txt       REG        8,3   146236  294089 /usr/bin/lsof
lsof      26305 jorge  mem       REG        8,3    46816  276251 /usr/lib/libnss_files-2.18.so
lsof      26305 jorge  mem       REG        8,3  1607632  294020 /usr/lib/locale/locale-archive
...
```

### List processes by PID

```
$ lsof -n -p 25 -p 45
```

### List processes by name

```
$ lsof -n -c urxvt
COMMAND  PID  USER   FD      TYPE             DEVICE SIZE/OFF    NODE NAME
urxvtd  1749 jorge  txt       REG                8,1  1299408 3431669 /usr/bin/urxvtd
urxvtd  1749 jorge  mem       REG                8,1    51808 3411196 /usr/lib/libnss_files-2.18.so
```

### List connections to a file

```
$ sudo lsof /var/log/Xorg.0.log
COMMAND PID USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
X       400 root    0w   REG    8,1    30052 7602187 /var/log/Xorg.0.log
```

### HUP a process

```
$ kill -HUP `lsof -t -c chrome`
```

### Kill all of a user’s processes

```
$ kill -9 `lsof -t -u jorge`
```

### List all open files without a link

```
$ lsof +L1
```

These files have been deleted, but one or several processes are keeping the files open. Though it might appear that the files are deleted from the filesystem, both the files and the blocks are being preserved.

These are just a few of the things you can do with **lsof**. I hope this tutorial has made you a little bit smarter!
