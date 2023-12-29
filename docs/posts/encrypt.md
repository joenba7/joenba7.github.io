---
title: encrypt
date: 2007-04-24
authors:
    - jorge
categories:
    - Snippets
---
```
#!/usr/bin/perl

# Shows your encrypted password

print "Enter your password: ";
chomp($password = );
$salt = sprintf("%02x",($$^time^rand)&0xFF);
print "Your encrypted password is: ".crypt($password,$salt)."\n";
```