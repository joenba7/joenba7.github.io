---
title: Password protecting folders with nginx
date: 2009-08-28
authors:
    - jorge
categories:
    - Linux
    - Snippets
---
Ever wondered how you can password protect a folder and the underlying content with **nginx** the same way Apache does? Pretty simple.

First create a htpass-file like so:

```
htpasswd -bc htpass NewUser NewPassword
```

Edit your site’s configuration file by adding the following lines of code inside the **server**-block:

```
location ^~ /secret {
    auth_basic "Restricted";
    auth_basic_user_file /etc/nginx/htpass;
}
```

And your folder should be password protected. :)