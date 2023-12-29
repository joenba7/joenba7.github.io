---
title: Test if a port on a remote system is reachable
date: 2016-01-07
authors:
    - jorge
categories:
    - Linux
tags:
    - bash
    - linux
    - netcat
    - port
    - telnet
    - test
---
With **telnet**:

```
telnet webserver.example.com 80
```

With **bash**:

```
cat < /dev/tcp/webserver.example.com/80
```

Replace **tcp** with **udp**, depending on what you want.

With **netcat**:

```
nc webserver.example.com 80 &> /dev/null; echo $?
```

If the port is open, you will get an output of 0. If it’s closed, it’s a **1**.
