---
title: Nginx, PHP, MySQL and WordPress continued
date: 2010-08-20
authors:
    - jorge
categories:
    - Linux
---
So, my machine at home took the plunge. A couple of days ago while I was reorganising the livingroom (I was not bored, I actually love doing that stuff), I placed the server on the floor, as it usually was placed above the TV. Michael then decided to play a little ON/OFF-game with the server. The powersupply was fried, and I can’t really bother to buy a new one at this moment. So, it was finally time for me to buy a VPS from out there somewhere. My decision fell on , and they actually seem pretty good so far, so now to see if it will last.

My concern was though, **VPS**’ (Virtual Private Server) aren’t usually that good, they have limited diskspace and hardware. I don’t need much of diskspace, but after some experience with Apache, I know it’s a huge memory-hog. So what I basically needed was **Apache**, **MySQL**, **PHP** and **WordPress**. I didn’t think it was going to work out, until a nice co-worker of mine tipped me off about **nginx**.

This was supposedly a very good, and very lightweight webserver, according to him. So I decided to give it a shot. And so far, it’s working wonders.

Here I’ll try to explain how to set up your server with **nginx**, **MySQL**, **PHP** and **WordPress**.

FastCGI
-------

FastCGI is what your webserver uses to interact with PHP-code, and this is not something that comes packaged with **nginx**.

There are two options you can use, and I have tested both. **spawn-fcgi** or **php-fpm**. I ended up using **php-fpm**, though **spawn-fcgi** is quicker to set up. I’ll try to explain how to set up both.

### spawn-fcgi

**spawn-fcgi**, which came with the webserver **lighttpd**. You usually had to download **lighttpd** first, compile it, and then grab the program from there. But for your convenience, you can [get the spawn-fcgi here](http://jorge.fbarr.net/files/spawn-fcgi), or just go to , and grab the source there to compile on your own (seems to be an own project now).

So basically, as root, do the following:

```
wget http://jorge.fbarr.net/files/spawn-fcgi
mv spawn-fcgi /usr/local/bin
```

Now, the next step is to make this program run at boot. For those of you using a RedHat’ish type of system, adding this to the end of **/etc/rc.d/rc.local** will do the trick. You can add it by running this command:

```
echo '/usr/local/bin/spawn-fcgi -f /usr/bin/php-cgi -a 127.0.0.1 -p 9000 -P /var/run/fastcgi-php.pid -u nginx -g nginx' >> /etc/rc.d/rc.local
```

**php-cgi** is the name of the fcgi-application, which is usually provided by your distribution’s PHP-package (I believe it’s **php-common** for CentOS/Fedora, so make sure you have it installed), and is referred to by the **-f** in the command. **-a** makes you specify which address to listen on, **-p** is the port (9000 in this case), and **-P** is the file to place the process ID in when the program is started, which makes it easier to kill the program if necessary.

Once placed in the **rc.local**-file, just run it manually to fire it up:

```
/usr/local/bin/spawn-fcgi -f /usr/bin/php-cgi -a 127.0.0.1 -p 9000 -P /var/run/fastcgi-php.pid -u nginx -g nginx
```

### php-fpm

**php-fpm** is a patch for php4/5 to greatly improve FastCGI SAPI usage in production. This means that you don’t install **php** through your distribution’s package manager, but rather download the package from the PHP-website, and the patch from another site. You patch up the sourcecode, compile things, and get it started.

One of the dependencies for how we’re going to compile PHP is MySQL, so install that first by typing:

```
yum install mysql mysql-devel
```

Then get the PHP-sourcecode by typing:

```
cd /usr/local/src
wget http://no2.php.net/get/php-5.3.0.tar.bz2/from/no.php.net/mirror
```

And the patch itself:

```
wget http://php-fpm.org/downloads/php-5.3.0-fpm-0.5.12.diff.gz
```

Then execute the following command:

```
tar -jxvf php-5.3.0.tar.bz2
```

Now we patch the newly downloaded PHP:

```
gzip -cd php-5.3.0-fpm-0.5.12.diff.gz | patch -d php-5.3.0 -p1
```

And enter the new directory to start compiling things:

```
cd php-5.3.0/
./configure --enable-fpm --with-mcrypt --enable-mbstring --with-mysql=/usr/include/mysql --with-mysql-sock=/tmp/mysql.sock --with-curl --enable-sockets --with-gd --with-zlib --with-iconv --with-jpeg-dir=/usr/lib
```

**Notice**: If you are using a 64-bit system you have to add in an **–with-libdir=64** for it to compile correctly. According to [Raja Varma](http://www.rajavarma.com/) you need to use **–with-libdir=lib64** for it to compile properly in a CentOS 64 bit installation. Thanks for the tip Raja!

At this point the compile failed quite a bit, as I had to install several more packages. The packages I had to install were the following (a snippet taken from yum.log):

```
Apr 23 08:34:44 Installed: zlib-devel-1.2.3-3.i386
Apr 23 08:34:50 Installed: libxml2-devel-2.6.26-2.1.2.7.i386
Apr 23 08:37:10 Installed: keyutils-libs-devel-1.2-1.el5.i386
Apr 23 08:37:18 Installed: libsepol-devel-1.15.2-1.el5.i386
Apr 23 08:37:23 Installed: libselinux-devel-1.33.4-5.1.el5.i386
Apr 23 08:37:30 Installed: e2fsprogs-devel-1.39-20.el5.i386
Apr 23 08:37:35 Installed: libidn-devel-0.6.5-1.1.i386
Apr 23 08:37:40 Installed: krb5-devel-1.6.1-31.el5_3.3.i386
Apr 23 08:37:46 Installed: openssl-devel-0.9.8e-7.el5.i386
Apr 23 08:38:07 Installed: curl-devel-7.15.5-2.1.el5_3.4.i386
Apr 23 08:40:15 Installed: libjpeg-devel-6b-37.i386
Apr 23 08:41:54 Installed: 2:libpng-devel-1.2.10-7.1.el5_3.2.i386
Apr 23 08:43:38 Installed: libmcrypt-2.5.8-4.el5.centos.i386
Apr 23 08:44:37 Installed: libmcrypt-devel-2.5.8-4.el5.centos.i386
```

Once everything compiles successfully, we continue with:

```
make all
make install
```

Now that everything is ready to go, we only need to modify two lines in the file **/usr/local/etc/php-fpm.conf**. Simply replace **nobody** with **nginx**, and remove the comments, making the lines look like this:

```
            Unix user of processes
            nginx               

            Unix group of processes
            nginx
```

And that should be it. Fire up **php-fpm** with:

```
/usr/local/bin/php-cgi --fpm
```

Making an init-script for **php-fpm** is pretty easy – simply make a symlink that points to **/usr/local/php/sbin/php-fpm**:

```
cd /etc/rc.d/init.d
ln -s /usr/local/sbin/php-fpm php-fpm
```

Now add the following line to **/etc/rc.d/rc.local** to make **php-fpm** start up at boot:

```
/usr/local/sbin/php-fpm
```

And you’re done!

Now to the next item on the list, **nginx**.

nginx
-----

**nginx** (pronounced as “engine X”) is a lightweight, high performance web server/reverse proxy and e-mail (IMAP/POP3) proxy HTTP server and mail proxy server written by Igor Sysoev.

So based on that we’re using CentOS now, we are going to get the necessary package from the EPEL-repository. Run these commands to fetch the package, and to install it:

```
wget http://download.fedora.redhat.com/pub/epel/testing/5/i386/nginx-0.6.38-1.el5.i386.rpm
yum --nogpgcheck localinstall nginx-0.6.38-1.el5.i386.rpm
```

Once installed, we need to configure three files, mainly. **/etc/nginx/fastcgi\_params**, **/etc/nginx/nginx.conf** and **/etc/nginx/conf.d/virtual.conf** (if you want to have several virtual domains). The first file, **fastcgi\_params**, we just need to add a little something. Open up the file and add the following line to it, right under SCRIPT\_NAME:

```
fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
```

Save and close the file.

This is what my **nginx.conf** pretty much looks like:

**Notice**: According to :

> …do not use the given nginx,conf, it is malicious point your server to his site.

Let me just say again, this is what ***MY*** configuration file looks like. I’ve simply pasted it so that you can see how a working configuration looks. So if you want it to work for you, you need to modify the values to suit your setup. I thought that was needless to say, but guess not. :)

```
#######################################################################
#
# This is the main Nginx configuration file.  
#
# More information about the configuration options is available on 
#   * the English wiki - http://wiki.codemongers.com/Main
#   * the Russian documentation - http://sysoev.ru/nginx/
#
#######################################################################

#----------------------------------------------------------------------
# Main Module - directives that cover basic functionality
#
#   http://wiki.codemongers.com/NginxMainModule
#
#----------------------------------------------------------------------

user              nginx;
worker_processes  1;

error_log         /var/log/nginx/error.log;
#error_log        /var/log/nginx/error.log  notice;
#error_log        /var/log/nginx/error.log  info;

pid               /var/run/nginx.pid;

#----------------------------------------------------------------------
# Events Module 
#
#   http://wiki.codemongers.com/NginxEventsModule
#
#----------------------------------------------------------------------

events {
    worker_connections  1024;
}

#----------------------------------------------------------------------
# HTTP Core Module
#
#   http://wiki.codemongers.com/NginxHttpCoreModule 
#
#----------------------------------------------------------------------

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] $request '
                      '"$status" $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    proxy_read_timeout 300;

    #gzip  on;

    # Load config files from the /etc/nginx/conf.d directory
    include /etc/nginx/conf.d/*.conf;

    #
    # The default server
    #
    server {
        listen       80;
        server_name  chihiro.fbarr.net;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

        error_page  404              /404.html;
        location = /404.html {
            root   /usr/share/nginx/html;
        }

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:20080
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    include        fastcgi_params;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }
}
```

The only thing I’ve changed here is **server\_name**, which I’ve set to chihiro.fbarr.net. This is like the main server we’re working with. Now I’m going to want to set up the virtual host jorge.fbarr.net on this machine as well. For that we need to configure the file **/etc/nginx/conf.d/virtual.conf**. This is what mine looks like:

```
#
# A virtual host using mix of IP-, name-, and port-based configuration
#

## jorge.fbarr.net
server {
    listen       80;
    server_name  jorge.fbarr.net;

    port_in_redirect off;

    root   /var/www/jorge.fbarr.net;

    proxy_redirect     off;
    proxy_redirect     http://jorge.fbarr.net:80/  /;

    access_log /var/log/nginx/jorge.fbarr.net-access.log;
    error_log  /var/log/nginx/jorge.fbarr.net-error.log;

    include /etc/nginx/fastcgi_params;

    index   index.php;

    if (!-e $request_filename) {
        rewrite ^(.+)$ /index.php?q=$1 last;
    }

    location ~ .*\.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
    }
}

## jorge.ulver.no redirect

server {
    listen      80;
    server_name jorge.ulver.no;

    rewrite ^(.*) http://jorge.fbarr.net$1 permanent;
}
```

That’s pretty much it. Everything should be working just fine by now. :) But we are lacking two things, **MySQL** and **WordPress**.

MySQL and WordPress
-------------------

To install **MySQL**, type up the following:

```
yum install mysql-server
```

As for how to set up **MySQL**, if you follow [these instructions](http://codex.wordpress.org/Installing_WordPress), it will tell you how you need to set up your database to work with **WordPress**, so I won’t be posting details about that here.

For **PHP** to be able to work with **MySQL** (if you have chosen to use **spawn-fcgi** and not **php-fpm**), you need to install a package called **php-mysql**. Install the package with **yum**, kill php-cgi, and start it again by doing these following things:

```
yum install php-mysql
pgrep php-cgi | xargs kill
/usr/bin/spawn-fcgi -f /usr/bin/php-cgi -a 127.0.0.1 -p 9000 -P /var/run/fastcgi-php.pid -u nginx -g nginx
```

Once everything is done, voila! You should now have **nginx**, **PHP**, **MySQL** and **WordPress** working together. And as you probably notice, **nginx** is taking a LOT less resources than **Apache** would have.

Issues with PHP
---------------

If you notice that errors such as the following are popping up on your site:

> It is not safe to rely on the system’s  
> timezone settings. Please use the date.timezone setting, the TZ  
> environment variable or the date\_default\_timezone\_set() function.

You can do the following:

```
cp /usr/local/src/php-5.3.0/php.ini-production /usr/local/lib/
```

Then open up the file **php.ini** and change:

```
;date.timezone =
```

to

```
date.timezone = "Europe/Oslo"
```

If you happen to live in Oslo. Change it to whatever is right for you really. Also, find the line that says something like:

```
error_reporting = E_ALL & ~E_NOTICE
```

And change it to:

```
error_reporting = E_ALL & ~E_DEPRECATED
```

Restart **php-fpm** and **nginx** and things should be back to normal.