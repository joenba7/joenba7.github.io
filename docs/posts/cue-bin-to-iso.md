---
title: .cue/.bin to .iso
date: 2009-06-23
authors:
    - jorge
categories:
    - Snippets
---
First install the program called **bchunk**:

```
yum -y install bchunk
```

Now we convert **.bin**/**.cue** to **.iso**:

```
bchunk foo.bin foo.cue foo.iso
```

And there you go.
