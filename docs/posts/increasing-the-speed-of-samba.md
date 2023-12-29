---
title: Increasing the speed of Samba
date: 2009-07-17
authors:
    - jorge
categories:
    - Linux
---
Often found that Samba is slow, for some reason?

The socket option **TCP\_NODELAY** is the one that seems to make the biggest single difference for most networks. Many people report that adding **socket options = TCP\_NODELAY** doubles the read performance of a Samba drive. The best explanation I have seen for this is that the Microsoft TCP/IP stack is slow in sending TCP ACKs.

Simply add this following line under the **\[global\]**-section of your **smb.conf**:

```
ocket options = TCP_NODELAY  IPTOS_LOWDELAY SO_SNDBUF=65535 SO_RCVBUF=65535
```

And restart the service. Notice the difference?

If not, it might be a problem with Windows. Often a speed problem can be traced to the client. The client (for example Windows for Workgroups) can often be tuned for better TCP performance. Check out [this article](http://us1.samba.org/samba/docs/man/Samba-HOWTO-Collection/Other-Clients.html) to find out more about what you can do to increase the speed.

Good luck!

