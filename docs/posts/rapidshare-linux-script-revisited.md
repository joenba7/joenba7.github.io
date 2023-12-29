---
title: Rapidshare Linux script revisited
date: 2010-09-16
authors:
    - jorge
categories:
    - Linux
    - Snippets
---
**Update**: I just added a new variable in the script, **DAYS**, which specifies how many days the cookie you create is valid for.  
**Update 2011-09-13**: Seems **Rapidshare** have been tampering with their API, which made the cookie-generating script useless. I basically changed the **getaccountdetails\_v1** with **getaccountdetails**, and the script is working fine now. I’ve fixed the script below to reflect this.

The code to get the Rapidshare-cookie in my previous post about this subject, Rapidshare Linux Script, unfortunately doesn’t work anymore. It seems that Rapidshare has moved from using https://ssl.rapidshare.com/premzone.html to https://ssl.rapidshare.com/cgi-bin/premiumzone.cgi, so things just don’t work as they used to. This is for people with a premium account.

However, there’s a solution. First off, log in to your Rapidshare-account, go to **Settings** and make sure to check the box named **Direct downloads, requested files are saved without redirection via RapidShare**.

To grab the cookie you need, place the piece of code below in a file, save it, do a **chmod +x** on it. Just remember to replace **MyUserName** and **MyPassword** with your username and password before you run the script:

Note: The script requires **curl** to be installed.

```
#!/bin/bash
# Script to get your Rapidshare-cookie, nom nom
# Jorge Enrique Barrera

# Variables you can change
USERNAME="MyUserName" # Username to your Rapidshare premium-account
PASSWORD="MyPassword" # Password
COOKIEJAR="/home/jorge/.cookies" # Where the cookies are to be placed
DAYS="30" # The number of days the cookie is valid for

## Do not change anything below here ##

DATA="sub=getaccountdetails&withcookie;=1&type;=prem&login;=$USERNAME&password;=$PASSWORD"
COOKIE_STRING=$(curl -s --data "$DATA" "https://api.rapidshare.com/cgi-bin/rsapi.cgi" | grep cookie | cut -c 8-)
COOKIE=".rapidshare.com TRUE / FALSE $(($(date +%s)+24*60*60*$DAYS)) enc $COOKIE_STRING"

if [ ! -d $COOKIEJAR ]; then
        echo "Creating the directory $COOKIEJAR."
        mkdir $COOKIEJAR
        echo "Done."
fi

echo "$COOKIE" | tr ' ' '\t' > $COOKIEJAR/rapidshare
```

Now that the cookie is present, you can proceed by downloading the files you want. Which way you do that, is up to you.

Either way I will paste two scripts, one with **wget** and the other one with **aria2**, and you can then make your pick. Just place the URL of the files you want to download in **/home/YourUsername/Downloads/.url**, one in each line:

wget
----

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

aria2
-----

```
#!/bin/bash
cd ~/Downloads
aria2c -j 5 -c --load-cookies=/home/jorge/.cookies/rapidshare -i /home/jorge/Downloads/.url
```

Enjoy. :)