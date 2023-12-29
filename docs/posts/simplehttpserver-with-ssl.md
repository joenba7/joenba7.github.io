---
title: SimpleHTTPServer with SSL
date: 2017-06-11
authors:
    - jorge
categories:
    - Linux
tags:
    - basehttpserver
    - linux
    - python
    - simplehttpserver
    - ssl
---

```
python -m SimpleHTTPServer
```

and the result:

```
jorge@applepie:~ $ python -m SimpleHTTPServer 8080
Serving HTTP on 0.0.0.0 port 8080 ...
```

It listens on all IPv4 interfaces, and binds to the port you specify, which in my case is **8080**. The person on the other side will then be able to access the files in the directory from the outside by going to **http://server1.example.com:8080**, provided that your machine has the hostname **server1.example.com**, and that you have the port **8080** forwarded to the IP of **server1**.

But what if you want to provide a secure connection, say over SSL? **SimpleHTTPServer** has no built in way of doing this.

But behold **ssl**, Python’s built in SSL-module!

To create a secure connection for your **SimpleHTTPServer**, first create a self signed certificate by running the following command (if you don’t have a proper SSL-certificate, that is):

```
openssl req -x509 -newkey rsa:4096 -keyout server1.example.com.key -out server1.example.com.pem -days 365 -nodes
```

Now create a script named **shttps.py** that contains the following code:

```
#!/usr/bin/env python

import BaseHTTPServer, SimpleHTTPServer
import ssl

## Variables you can modify

bind_to_address = ''
server_port = 8080
ssl_key_file = "/etc/ssl/certs/localcerts/server1.example.com.key"
ssl_certificate_file = "/etc/ssl/certs/localcerts/server1.example.com.pem"


## Don't modify anything below

httpd = BaseHTTPServer.HTTPServer((bind_to_address, server_port), SimpleHTTPServer.SimpleHTTPRequestHandler)
httpd.socket = ssl.wrap_socket (httpd.socket, server_side=True,
                                keyfile=ssl_key_file,
                                certfile=ssl_certificate_file)
httpd.serve_forever()
```

The only thing that needs further explanation is the variable **bind\_to\_address**. Fill this in with the text **localhost** if you want it to only listen to **127.0.0.1**. Leave it blank to have it listen to all IPv4 interfaces (**0.0.0.0**).

Now that the certificate and key is all in place, and the script has been created, make it executable with:

```
chmod +x shttps.py
```

Go to the folder you’d like to share the contents of, and run the script:

```
jorge@applepie:~ $ ls
foo/ shttps.py
jorge@applepie:~ $ cd foo
jorge@applepie:~/foo $ ls
hello.txt world.txt
jorge@applepie:~/foo $ ../shttps.py
```

The result when you visit **https://server1.example.com:8080**?[![](../../../../uploads/2017/06/simplehttps_script_result.png)](https://jorge.fbarr.net/wp-content/uploads/2017/06/simplehttps_script_result.png)Because there is no third party verification it’s listed as insecure, but it should do the trick well enough for sharing files with others.

If you however do want a free SSL certificate for a more permanent setup, I suggest [LetsEncrypt](https://letsencrypt.org/getting-started/)! Check out [https://letsencrypt.org/getting-started/ ](https://letsencrypt.org/getting-started/)for more information.
