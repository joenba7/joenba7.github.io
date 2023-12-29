---
title: How to unregister Viscosity
date: 2020-03-17
authors:
    - jorge
categories:
    - Apple
tags:
    - license
    - macOS
    - unregister
    - viscosity
    - vpn
---
I was helping a colleague of mine set up VPN, with the client **Viscosity** to be specific, on **macOS**. During the process of setting it up, we noticed that remains of an old license, from another place, was still there. When she tried connecting to one of our remote servers, she got the following error:

> This copy of Viscosity is registered using a license that does not allow connections to one or more of the remote servers for this connection. Please contact your VPN Provider for further details.

Tried searching for options to unregister the old license, but there was nothing visible in the GUI. I finally found a solution though, but it needs to be done in the terminal.


```
defaults delete com.viscosityvpn.Viscosity License
```

Then press **Enter**. Now start **Viscosity** again, and it should no longer give you that error. If your trial period has ended, you will have to enter a new valid license.
