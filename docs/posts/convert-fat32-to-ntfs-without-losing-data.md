---
title: Convert FAT32 to NTFS without losing data
date: 2014-04-13
authors:
    - jorge
categories:
    - Windows
tags:
    - convert
    - fat32
    - ntfs
    - 'too large for the destination file system'
---
I had a USB-stick formatted with **FAT32**, and when I tried transferring a file over 4GB to it, I suddenly got the error:

> The file ‘example.iso’ is too large for the destination file system.

The file system **FAT32** only supports file sizes of up to 4GB, thus this error. Luckily there’s an easy way to fix this – convert the file system from **FAT32** to **NTFS**. This can be done easily with the command **convert**, and should not cause you to lose data on the device you’re converting, or have to format it. However, backup is **always** adviced.

So, the conversion itself:

1\. Click on the Start-menu  
2\. Type **cmd** in the search bar  
3\. Check the disk for errors with the command:

```
chkdsk e: /f
```

Where **e:** is the letter of the drive of the USB-stick.

4\. Execute the conversion with:

```
convert e: /FS:NTFS
```

The conversion-process will take a few minutes, and in the end the program will inform you when the conversion was successful.

That’s all there is to it. :)
