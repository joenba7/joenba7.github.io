---
title: VMware, i3 and multiple monitors
date: 2016-12-12
authors:
    - jorge
categories:
    - Linux
    - Windows
tags:
    - cycle
    - i3
    - linux
    - monitor
    - open-vm-tools
    - vmware
    - vmware-tools
---
For a while now I’ve been trying to set up VMware to work with multiple monitors, in a Linux guest. With some windowmanagers it works out of the box without any issue, such as with **Unity**. I never figured out how to do it with **xmonad**, and recently I switched to **i3** just to try something new. The damn **“Cycle multiple monitors”** button didn’t work here either. When I tried it, a message popped up saying:

> The virtual machine must have up-to-date VMware Tools installed and running.

<!-- more -->

..which it had! At this point I had installed **vmware-tools**, which is described as:

> “A suite of utilities that enhances the performance of the virtual machine’s guest operating system and improves management of the virtual machine”

However, I found a solution! Place the following line in your **i3** configuration file, whether it be **~/.i3/config** or **~/.config/i3/config**:

```
exec --no-startup-id vmware-user
```

..and that’s it! Reload your **i3** configuration, and now you should be able to press the **“Cycle multiple monitors”** button and have dual monitors in your VMware guest!

Now, if you’re using **open-vm-tools** instead of the native **vmware-tools**, do what **Fabian** suggested – use the following line in your config-file instead:

```
exec -–no-startup-id vmware-user-suid-wrapper
```

It should work!
