---
title: Accelerating OpenSSH connections
date: 2009-12-01
authors:
    - jorge
categories:
    - Linux
---
You’ve probably been in the situation before where you have to connect to a server several times, which means that you most likely have to re-authenticate each time. There are several workarounds to this. You can use **ssh-keys**, or a **socket** to bypass it. How? In this round we’ll learn about sockets.

Open up your **/etc/ssh/sshd\_config** and find the line **Host** \*. Right below it, paste the following:

```
ControlMaster auto
ControlPath ~/.ssh/socket-%r@%h:%p
```

And voila, that should speed up your OpenSSH-connection a bit, not to mention you won’t have to re-authenticate each time. Example?

First time:
-----------

```
ssh jorge@totoro
jorge@totoro’s password:
Last login: Mon Dec 14 08:24:54 2009 from home.example.com
[jorge@totoro ~]$
```

Second time:
------------

```
ssh jorge@totoro
Last login: Mon Dec 14 08:24:54 2009 from home.example.com
[jorge@totoro ~]$
```

As you can see from the first try we have to authenticate at least once. When done with that, you SSH to the same server, without the need to authenticate again. Without the sockets-setup you’d have to authenticate twice. Nifty huh?