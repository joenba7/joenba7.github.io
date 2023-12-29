---
title: Generate a htpasswd password without installing Apache
date: 2009-08-31
authors:
    - jorge
categories:
    - Linux
    - Snippets
---
A oneliner with Perl does the trick:

```
perl -le 'print crypt("password", "salt")'
```

The output you get should be placed in a file called **htpasswd**. The syntax is like so:

```
UserName:EncryptedPassword:YourComment
```