---
title: Nginx - Error 413 - Request entity too large
date: 2011-04-03
authors:
    - jorge
categories:
    - Linux
---
The default maximum body size of a client request, or maximum file size, that **nginx** allows you to have, is 1M. So when you try to upload something larger than 1M, you get the following error:

> 413: Request Entity Too Large.

Changing your **php.ini**-settings alone won’t do much. So to fix this problem, follow these steps.

Open up your **nginx.conf**-file, with an editor of your choice:

```
vim /etc/nginx/nginx.conf
```

..and add **client\_max\_body\_size** to the **http**-section:

```
client_max_body_size 20M;
```

Save and close the file, apply the changes to **nginx**, and reload:

```
/usr/sbin/nginx -t
/usr/sbin/nginx -s reload
```

Try uploading again. Should be working. :)
