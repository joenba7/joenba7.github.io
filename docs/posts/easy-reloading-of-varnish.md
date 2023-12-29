---
title: Easy reloading of Varnish
date: 2009-05-27
authors:
    - jorge
categories:
    - Linux
---
A co-worker of mine made a nice little script to make reloading of the default VCL that Varnish uses a bit easier. Not wanting to telnet or use varnishadm manually, this script does the trick. This was taken from http://kristian.blog.linpro.no/2009/02/18/easy-reloading-of-varnish-vcl/:

```
#!/bin/bash
# Reload a varnish config
# Author: Kristian Lyngstol

FILE="/etc/varnish/default.vcl"

# Hostname and management port
# (defined in /etc/default/varnish or on startup)
HOSTPORT="localhost:6082"
NOW=`date +%s`

error()
{
    echo 1>&2 "Failed to reload $FILE."
    exit 1
}

varnishadm -T $HOSTPORT vcl.load reload$NOW $FILE || error
varnishadm -T $HOSTPORT vcl.use reload$NOW || error
echo Current configs:
varnishadm -T $HOSTPORT vcl.list
```
