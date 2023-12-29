---
title: Making your BASH history more efficient
date: 2011-03-24
authors:
    - jorge
categories:
    - Linux
---
There are several ways to improve how you troubleshoot a system. One of the things I consider to be important is the BASH history. Here you can see what commands were executed. Though as default you can’t see when, or ignore certain things, like duplicate commands. Here’s a little guide to show you how.

**Notice**: To make these features a permanent solution, open up your **/etc/profile**, and add the lines to the bottom of the file. Save and close it. The next time you log in the functions should be working as you specified them!

HISTTIMEFORMAT
--------------

A simple yet effective little trick is to change the format of the output the command history spits out. In my case it comes to good use when troubleshooting a system, wanting to find out if errors in the system log match up with commands executed at that time. To enable the feature in your current terminal, run:

```
export HISTTIMEFORMAT="%h %d %H:%M:%S> "
```

So the output from history that was looking like this:

```
[jorge@ashitaka ~]$ history
1 ls
2 echo "Hello"
3 pwd
...
```

Now looks like this:

```
[jorge@ashitaka ~]$ history
1 Mar 24 12:53:35> ls
2 Mar 24 12:53:36> echo "Hello"
3 Mar 24 12:53:37> pwd
...
```

HISTCONTROL
-----------

The variable **HISTCONTROL** can do a few things, like ignoring duplicates if they are in a row and erasing duplicates. The difference here is that by ignoring duplicates, it will remove duplicates only if they are in a row, while erasing duplicates, it will remove previous commands from the history file. Let’s give an example:

### ignoredups

First run:

```
export HISTCONTROL=ignoredups
```

The output that looked like this:

```
[jorge@ashitaka ~]$ history
1 pwd
2 echo "Hello"
3 ls -l
4 ls -h
5 pwd
6 pwd
...
```

Will now look like this:

```
[jorge@ashitaka ~]$ history
1 pwd
2 echo "Hello"
3 ls -l
4 ls -h
5 pwd
...
```

### erasedups

**erasedups** means that if you executed the same command previously, that entry will be erased, substituted by the newly run command. Generally not recommended though. If you still wish to do so, it can be done by running the following line:

```
export HISTCONTROL=erasedups
```

So a history that looked like this:

```
[jorge@ashitaka ~]$ history
1 pwd
2 echo "Hello"
3 ls -l
4 ls -h
5 pwd
...
```

After the export, will now look like this when we run the command **pwd** again:

```
[jorge@ashitaka ~]$ history
1 echo "Hello"
2 ls -l
3 ls -h
4 pwd
...
```

### ignorespace

If you want full control over which commands will be stored by history and which will not be stored, set **HISTCONTROL** to **ignorespace**:

```
export HISTCONTROL=ignorespace
```

So out of these commands:

```
ls -la
 du -h
ps
```

Only these will show:

```
[jorge@ashitaka ~]$ history
1 ls -la
2 ps
...
```

### ignoredups and ignorespace

To use both of these features, simply use:

```
export HISTCONTROL=ignoreboth
```

HISTIGNORE
----------

The variable **HISTIGNORE** will help you tell history which commands to ignore, meaning that they shouldn’t be picked up by the BASH history. For instance, let’s say that I want the commands **df** and **free** to be ignored, including optional flags:

```
export HISTIGNORE="df*:free*"
```

HISTSIZE
--------

Setting how many lines of history to store is always a nice feature:

```
export HISTSIZE=10000
```

Thanks to [pkhamre](http://blog.pkhamre.com "pkhamre's blog") for suggesting to include **HISTCONTROL** and **HISTIGNORE**. ;)
