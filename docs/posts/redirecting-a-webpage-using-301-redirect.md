---
title: Redirecting a webpage using 301 redirect
date: 2008-08-04
authors:
    - jorge
categories:
    - Linux
---
Let’s say that someone on the Internet has been linking to one of your webpages, but for some reason you change the system you use (Changing from Drupal to WordPress, for instance), or just ended up moving the page somewhere else. Sending an email to the people linking to your site doesn’t guarantee that they’ll take the time to point to the new URL of the page. So what can YOU do? Use *.htaccess* and a 301 Redirect. I’ll not explain here where the file is to be placed, or how you configure Apache to take it into consideration.

Place the following bit of code in your *.htaccess*:

```
Redirect 301 /old-location/file.html http://www.new-location.com/file.html
```

Easy peasy.

