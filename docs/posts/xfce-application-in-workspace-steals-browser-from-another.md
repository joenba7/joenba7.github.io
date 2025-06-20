---
title: XFCE - Application in workspace steals browser from another
date: 2025-06-20
authors:
    - jorge
categories:
    - Linux
tags:
    - xfce
    - browser
    - xconf-query
    - xfwm4
---

This problem has been bugging me for some time. I am currently running the XFCE spinoff of Fedora 42, and I have multiple workspaces set up. If I click a link on my workspace that has my e-mail client, it steals the browser my Browser-workspace, and moves it to my e-mail workspace. The fix? Pretty simple.

<!-- more -->

Run the command:

```bash
xconf-query -c xfwm4 -p /general/activate_action -s switch
```

What this does:

* **xfconf-query** — a command-line tool to query or set Xfce settings.
* **-c xfwm4** — selects the channel (in this case, settings related to the Xfce Window Manager, xfwm4).
* **-p /general/activate_action** — specifies the property to modify (here: what happens when a window is activated).
* **-s switch** — sets the value of the property to switch.

After you have run the command, links clicked in the e-mail workspace (or whatever workspace) will now switch to the Browser-workspace and open the link there instead of dragging it to where the source was. In my case, the e-mail workspace.
