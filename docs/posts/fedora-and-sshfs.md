---
title: Fedora and SSHFS
date: 2009-04-28
authors:
    - jorge
categories:
    - Linux
---
I had heard about **sshfs** before, but didn’t attempt anything with it til today. And I find it pure genius. A little about **sshfs**.

This is a filesystem client based on the SSH File Transfer Protocol. Since most SSH servers already support this protocol it is very easy to set up: i.e. on the server side there’s nothing to do. On the client side mounting the filesystem is as easy as logging into the server with ssh.

The idea of sshfs was taken from the SSHFS filesystem distributed with LUFS, which I found very useful. There were some limitations of that codebase, so I rewrote it. Features of this implementation are:

- Based on FUSE (the best userspace filesystem framework for linux ;-)
- Multithreading: more than one request can be on it’s way to the server
- Allowing large reads (max 64k)
- Caching directory contents

After having aquired my new VPS, I wanted to mount something over **NFS** to my workstation at home. Being that the VPS is running on OpenVZ, it doesn’t allow for normal **NFS** to work. I tried something called **unfs3** instead, a user-space **NFS**-server. I ended up inventing new curse words yet again. So someone tipped me about **sshfs**, and it’s working like a dream. Mounting things is as easy as logging on to a remote system with regular **ssh**.

First install **sshfs**:

```
yum install fuse-sshfs
```

In some cases, after having installed **fuse-sshfs**, you will have to add the user to the group **fuse**. If that’s the case, you can do so like this:

```
gpasswd -a YourUserName fuse
```

Once you are in the group, you need to log out and in again for it to take effect. When logged in, you can mount things along the lines of:

```
sshfs user@server.example.com:/home/user/files /home/user/files
```

And that’s that! The only thing you have to worry about is that the folder you’re trying to mount on the remote machine has the same owner on your workstation. So the user **jorge** can pretty much mount anything on his workstation, from the remote machine, as long as it is owned by him.

To unmount something you can use:

```
fusermount -u /here/it/goes/
```

Enjoy!