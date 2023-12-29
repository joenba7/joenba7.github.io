---
title: Split a file into a number of equal parts
date: 2016-05-20
authors:
    - jorge
categories:
    - Linux
tags:
    - equal
    - linux
    - parts
    - split
    - tricks
    - tutorial
---
As an example, we have a file named **primary\_data\_file.txt** that contains 616 lines of data. We want to split this into 4 files, with the equal amount of lines in each.

```
$ wc -l primary_data_file.txt 
616 primary_data_file.txt
```

The following command should do the trick:

```
split -da 1 -l $((`wc -l < primary_data_file.txt`/4)) primary_data_file.txt split_file --additional-suffix=".txt"
```

The option **-da** generates the suffixes of length 1, as well as using numeric suffixes instead of alphabetical.

The results after running the command are the following files:

```
$ wc -l split_file*
  154 split_file0.txt
  154 split_file1.txt
  154 split_file2.txt
  154 split_file3.txt
  616 total
```
