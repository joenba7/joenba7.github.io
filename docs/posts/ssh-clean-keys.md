---
title: ssh-clean-keys
date: 2008-07-08
authors:
    - jorge
categories:
    - Snippets
---
Simple lines of code to clean invalid hosts from your *.known\_hosts*-file.

```
ssh-clean-keys () 
{ 
    for host in $1 $(dig +short $1);
    do
        echo "Cleaning $host";
        ssh-keygen -R $host 2>&1 | sed -e 's,^,I,g';
    done
}
```