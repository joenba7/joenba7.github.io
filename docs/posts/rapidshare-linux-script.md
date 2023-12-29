---
title: Rapidshare Linux script
date: 2010-03-26
authors:
    - jorge
categories:
    - Linux
---
**Update:** Seems this procedure isn’t working anymore, as Rapidshare has switched some things around. Have a look at [Rapidshare Linux Script Revisited](http://jorge.fbarr.net/2010/09/16/rapidshare-linux-script-revisited/) for updated information.

A friend tipped me about Rapidshare, and has been working great ever since I bought a premium account. Thing is though, most addons/download clients are specifically for Windows, and I use Fedora Linux. I searched quite a while before I found an option that makes downloading through a Rapidshare Premium Account damn simple; namely making a script. And it’s really easy!

What we first need to do is to grab and save your Rapidshare cookie. This is luckily something you only need to do once. Execute the following command to do so:

```
wget --save-cookies /home/jorge/.cookies/rapidshare --post-data "login=USERNAME&password=PASSWORD" -O - https://ssl.rapidshare.com/cgi-bin/premiumzone.cgi > /dev/null
```

..replacing **USERNAME** and **PASSWORD** with your Rapidshare account details, and **/home/jorge** with whatever suits you.

Once that is done, we proceed with the script itself. Now, most pages will recommend using **wget** for this, but I personally prefer **aria2**. This is because **aria2** supports multiple files download and it also splits each file in multiple parts for faster downloading. Moreover you can resume the download any time you want just by typing the same command again.

Either way I will paste two scripts, one with **wget** and the other one with **aria2**, and you can then make your pick.

### wget

```
#!/bin/bash
LIST="/home/jorge/Downloads/.url"
cd ~/Downloads
for url in `cat $LIST`
do
    wget -c --load-cookies /home/jorge/.cookies/rapidshare $url
done
```

Save this file as **wrsd.sh** and make it executable by doing a:

```
chmod +x wrsd.sh
```

### aria2

```
#!/bin/bash
cd ~/Downloads
aria2c -j 5 -c --load-cookies=/home/jorge/.cookies/rapidshare -i /home/jorge/Downloads/.url
```

Save this as **arsd.sh** and make it executable just like above. Now the file containing the various URLs to download is in my case located in the file **~/Downloads/.url**. One URL in each line should do the trick. Open the file, paste a few URLs, save and close it, and run either **wrsd.sh** or **arsd.sh**, depending on if you want to use **wget** or **aria2**.

Enjoy!