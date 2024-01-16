---
title: Convert Putty public key to OpenSSH public key
date: 2024-01-16
authors:
    - jorge
categories:
    - Snippets
tags:
    - linux
    - putty
    - ssh
    - public key
---

To convert a Putty public SSH key to an OpenSSH public key, run:

```bash
ssh-keygen -i -f PuttyPublicKey.pub > opensshpublickey.pub
```
