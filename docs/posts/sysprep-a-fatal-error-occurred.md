---
title: sysprep &#8211; a fatal error occurred
date: 2017-04-22
authors:
    - jorge
categories:
    - Windows
tags:
    - error
    - fatal
    - solution
    - sysprep
    - windows
    - 'windows deployment services'
---
I’m currently testing out **Windows Deployment Services**, and while working with **sysprep** on a Windows 7 Pro client machine, I got the following error:

[![](../../../../uploads/2017/04/sysprep-fatal-error.png)](https://jorge.fbarr.net/wp-content/uploads/2017/04/sysprep-fatal-error.png)

Nothing seemed to work, till I tried the following recipe:

Open the run-menu, type in **regedit** and go to **HKEY\_LOCAL\_MACHINE\\SYSTEM\\Setup\\Status\\SysprepStatus**. Find **GeneralizationState** and set the value to **7**.

Run a command prompt with administrative privileges. Type:

```
msdtc -uninstall
msdtc -install
```

Open up **regedit** again and find **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\SoftwareProtectionPlatform**. Find **SkipRearm** and set the value to **1**.


```
net stop WMPNetworkSvc
```

Try running **sysprep** again now. Hopefully it should work. That’ll save you some time of frustration and hopefully you won’t go bald sooner.

Should this not work, check the **sysprep**-log file at **C:\\Windows\\System32\\Sysprep\\Panther\\setuperr.txt**.
