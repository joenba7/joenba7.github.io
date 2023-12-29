---
title: Introduction to strace
date: 2014-01-19
authors:
    - jorge
categories:
    - Linux
tags:
    - calls
    - linux
    - signals
    - strace
    - system
    - trace
    - troubleshooting
---
There will come a time when you will find yourself asking “What the heck is that process doing?”. To uncover the mysteries behind the behaviour of a process, we have a tool called **strace**.

The program **strace** is very handy when you want to debug the execution of a program. It catches and states all the system calls performed called by a process. It will also catch and state any inter-process signals received by this process.

Let’s dive into some examples.

Trace the execution
-------------------

```
strace ls
```

..is the simple straight-forward way to use it. The output might look something like:

```
execve("/bin/ls", ["ls"], [/* 32 vars */]) = 0
brk(0)                                  = 0x1117000
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fe7c5bc2000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=69243, ...}) = 0
mmap(NULL, 69243, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fe7c5bb1000
close(3)                                = 0
open("/lib64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\240d\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=142136, ...}) = 0
mmap(NULL, 2242712, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fe7c5780000
mprotect(0x7fe7c57a1000, 2093056, PROT_NONE) = 0
...
```

The output looks a bit messy, but it can provide very useful information like which files does this program use, what is this program doing right now or why does this program not read the config file – does it even look for it?

### Attach to an existing process

You can run **strace** on a process that’s already running. Use **-p**, and provide the Process ID (PID):

```
pidof urxvtd
772
strace -p 772
```

The output presented to you is similar to the example above. Notice that you can only trace a process you have access rights to. Multiple **-p** options will also trace these processes with a limit of 32 processes that **strace** can attach to.

Trace child processes
---------------------

Use **-f** to trace child processes as they are created by currently traced processes. This is useful for debugging a program that spawns children. **strace** will prepend the pid of the traced process output:

```
strace -f ls
```

Trace specific system calls
---------------------------

```
strace -e open ls
```

The **e**-flag, along with the call **open**, displays only open system calls of the **ls** command, which outputs something along these lines:

```
...
open("/lib64/libdl.so.2", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libattr.so.1", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3
open("/proc/filesystems", O_RDONLY)     = 3
open("/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
ghost  ghost.zip
+++ exited with 0 +++
```

With the above example, close to the end, you can see two things – **ghost** and **ghost.zip**, a folder and file, which is the actual output of the command **ls**.

A few of the options available after **-e trace=** are:

- open
- close
- read
- write

### Trace multiple system calls

What if you want to trace multiple system calls in one command though? The option **-e trace=** can take a comma separated set of systemcalls as argument:

```
strace -e trace=open,read ls
```

Which outputs:

```
...
open("/lib64/libattr.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\320\23\0\0\0\0\0\0"..., 832) = 832
open("/lib64/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\340m\0\0\0\0\0\0"..., 832) = 832
open("/proc/filesystems", O_RDONLY)     = 3
read(3, "nodev\tsysfs\nnodev\trootfs\nnodev\tr"..., 1024) = 356
read(3, "", 1024)                       = 0
open("/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
ghost  ghost.zip
+++ exited with 0 +++
```

Write output to file
--------------------

If you’d like to write the output of **strace** to a file, add a **-o**:

```
strace -o ls.txt ls
ghost  ghost.zip
```

When you print the contents of **ls.txt**, you’ll see that it contains only the output from **strace**, and not from the command **ls**:

```
cat.ls.txt
...
write(1, "18.png\t\t   Apps  Declarations  d"..., 164) = 164
close(1)                                = 0
munmap(0x7f993acb6000, 4096)            = 0
close(2)                                = 0
exit_group(0)                           = ?
write(1, "18.png\t\t   Apps  Declarations  d"..., 164) = 164
close(1)                                = 0
munmap(0x7f993acb6000, 4096)            = 0
close(2)                                = 0
exit_group(0)                           = ?
+++ exited with 0 +++
```

Add a timestamp
---------------

If you add a **-t** to the command, you’ll be able to add a timestamp to each printed line. You can add up to 3 **-t**‘s. The more you add, the more verbose and detailed the timestamp will be.

One **-t** shows you seconds:

```
strace -t ls
...
11:52:20 close(2)                       = 0
11:52:20 exit_group(0)                  = ?
11:52:20 +++ exited with 0 +++
```

Adding a second **-t** will display microseconds:

```
strace -tt ls
...
11:53:07.593382 close(2)                = 0
11:53:07.593500 exit_group(0)           = ?
11:53:07.593651 +++ exited with 0 +++
```

A third **-t** will include the microseconds and the leading portion will be printed as the number of seconds since the epoch.

```
strace -ttt ls
...
```

### Print relative timestamp

If you’d like to find out the execution time of each call, **-r** will certainly do the trick:

```
strace -r ls
...
     0.000105 close(2)                  = 0
     0.000115 exit_group(0)             = ?
     0.000146 +++ exited with 0 +++
```

A summary of system calls
-------------------------

Would you like to have a summary of calls, time, and errors for each system call? **-c** does this:

```
strace -c ls
ghost  ghost.zip
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
100.00    0.000054          11         5           mprotect
  0.00    0.000000           0         4           read
  0.00    0.000000           0         1           write
  0.00    0.000000           0         6           open
  0.00    0.000000           0         9           close
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         1         1 access
  0.00    0.000000           0         3           brk
  0.00    0.000000           0         2           ioctl
  0.00    0.000000           0         2           munmap
  0.00    0.000000           0        15           mmap2
  0.00    0.000000           0         7           fstat64
  0.00    0.000000           0         2           getdents64
  0.00    0.000000           0         1           set_thread_area
  0.00    0.000000           0         1           openat
------ ----------- ----------- --------- --------- ----------------
100.00    0.000054                    60         1 total
```

This option is very useful when trying to find out why a program is running slow.
