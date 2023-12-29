---
title: No Wi-Fi device found
date: 2015-05-22
authors:
    - jorge
categories:
    - Linux
tags:
    - error
    - fedora
    - linux
    - NetworkManager
    - nmcli
    - 'no wi-fi'
---
I just reinstalled one of my machines with Fedora 21. The install went great, but when the machine was done rebooting, and I was trying to set up my access to a wireless network with **nmcli** (**NetworkManager**), I got the following error-message:

```
# nmcli dev
DEVICE   TYPE      STATE        CONNECTION 
enp0s25  ethernet  unavailable  --         
lo       loopback  unmanaged    --         
wlo1     wifi      unmanaged    --
```

..and:

```
# nmcli dev wifi rescan
Error: No Wi-Fi device found.
```

Spending a lot of time finding what the problem was, I finally found the solution. It seems that Wi-Fi support in **NetworkManager** has been separated to a plugin. The package **NetworkManager-wifi** was missing. According to **Pablo S. Torralba**, who was kind enough to comment on this post, it should be enough to kill **NetworkManager** and launch it again as **root**. Should that fail, you can always try the old reboot-trick! Either way, it should hopefully be working.
