---
title: Python password generator
date: 2010-09-15
authors:
    - jorge
categories:
    - Linux
    - Snippets
---
```
#!/usr/bin/env python
# Filename: passgen.py

import random, string

mystr = string.ascii_letters+string.digits
random_string = []

while len(random_string) <= 8:
    random_string.append(random.choice(mystr))
print "".join(random_string)
```