---
title: Ubuntu, Citrix, and SSL-error 61
date: 2012-04-03
authors:
    - jorge
categories:
    - Linux
---
I just attempted to install the newest **Linux Citrix ICA** client for a colleague, on Ubuntu, and upon a successful install, an error similar to this one popped up when trying to run the client from Firefox:

> SSL error  
> Contact your help desk with the following information:  
> You have not chosen to trust”/C=US/ST=/L=/O=Equifax/OU=Equifax Secure Certificate Authority/CN=”,  
> the issuer of the server’s security certificate ((SSL error 61).

The solution to this? Make Firefox’s certificates accessible to Citrix, like so:

```
sudo ln -s /usr/share/ca-certificates/mozilla/* /opt/Citrix/ICAClient/keystore/cacerts
```

That should do the trick!