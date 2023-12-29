---
title: Introduction to screen
date: 2011-02-09
authors:
    - jorge
categories:
    - Linux
---
**screen** is for many people an unknown program and tool, but those who do know it consider it to be an essential one. This is because of the functionality of the program, that can end up saving you a lot of work and time. You can run any number of console-based applications – editors, mail clients, browsers, in just a single terminal. Sometimes it’s useful to have a terminal for each console-based application you’re running, but sometimes not, obviously.

For my part, the main reason for me using **screen** is the ability to decouple the terminal emulator from the running programs. In other words, I can detach and reattach things as I please. To put it in simple terms; it’s a tool for session management. Let’s go ahead with an example.

You are logged into your remote server via **SSH**, working on some configuration file, when suddenly you lose connection to it. Almost done, having spent hours, you grab your laptop in anger and smack the closest unfortunate person next to you. Not only have you lost valuable work, but soon enough people with shields, guns and helmets will come after you. Imagine, with **screen**, it could have been avoided! Had you been editing the configuration file within a **screen**, and should you have lost connection, the editing would still be in an active screen session, allowing you to detach and reattach the screen to your terminal once you logged back in.

Let’s have a closer look at what we can do with screen.

Starting screen
---------------


```
screen
```

If installed, you probably won’t get a message about having entered a screen session, but I’m sure you have. Everything functions just like a normal shell, except for a few special characters. screen uses the command **Ctrl-A** as a signal to screen instead of the shell.

To see the help page for screen, press **Ctrl-A**, then press **?**. In other words:

```
^A ?
```

As you can see here, **Ctrl** is represented by the **^**, **A** is **A**, and well, you get the rest.

To start a program directly into screen, just type **screen** . Let’s use **top** as an example.

Using screen
------------

Fire it up:

```
screen top
```

**top** is started in the usual way, just inside a **screen** session. To detach the session and let it run in the background, use **Ctrl-a d**:

```
Ctrl-a d (^a d)
```

Once your session is detached you can list the available **screen** sessions by using:

```
screen -ls
```

The output you’ll see will be something along the lines of:

```
[jorge@cat ~]$ screen -ls
There is a screen on:
    26606.pts-39.cat    (Detached)
1 Socket in /var/run/screen/S-jorge.
```

To reattach the **screen** session to your current terminal, you can use **screen -dr** . In this case it would be:

```
screen -dr 26606
```

This brings up an interesting question though. How can you differentiate what multiple **screen** sessions are holding, if you can only read the process number? Easy – you give each **screen** session a name. For most people, a name is easier to remember than a number. So, we start a new **screen**, in which we’re going to open an IRC client called **irssi**. Let’s use **-S**:

```
screen -S irc irssi
```

Entering **irssi**, we detach the session with **Ctrl-a d**, and do a **screen -ls** to see what we’ve got now:

```
[jorge@cat ~]$ screen -ls
There are screens on:
    26606.pts-39.cat    (Detached)
    26702.irc    (Detached)
2 Sockets in /var/run/screen/S-jorge.
```

There you clearly see the name **irc** that we entered earlier, and the session holding **top**, which we started up even before being able to specify a name. Pretty handy, huh? This is particularly hand if you’re only going to have one application in the **screen** session. If more, it would probably be better to have a more descriptive session name of some sort.

Sharing a screen
----------------

If someone else, or you, has created a **screen**, and would like to share with someone else what you’re doing, then **screen -x** does the trick. Simply use:

```
screen -x irc
```

..if irc is the name of the session, or the process ID. Once done, you should have enabled multi display mode. And if you find out that the **screen** you have taken part of doesn’t match up to the size of your own terminal, simply use:

```
Ctrl-a F (^a F)
```

..to force it to match up to the dimensions.

Multiple applications in one session
------------------------------------

Now I’ve introduced you to the basics of using **screen**. Let’s proceed with using **screen** with multiple applications. Detach and resume the **screen** session called **irc** with:

```
screen -dr irc
```


```
nano
```

And the application will start. Let’s give this internal **screen** session a name. Press **Ctrl-a Shift-a (a A)**. Notice that **a** is different from **A** (capital A).

You will be asked:

```
Set window's title to:
```

Type **editor** and press enter.

Navigating within the screens
-----------------------------

You can switch between the various applications by pressing **Ctrl-a 0-9**, so:

```
Ctrl-a 0
```

will go to the first application, **irssi** in this case. Other ways of navigating is by using

```
Ctrl-c Ctrl-n (next window)

Ctrl-c Ctrl-p (previous window)
```


```
Ctrl-a " (^a ")
```

You’ll get a list that looks something like:

```
Num Name                                                                   Flags

  0 irssi                                                                      $
  1 nano                                                                       $
```

Use your Up/Down arrows and press enter to select a shell to go to.

Clearing destroyed sessions
---------------------------


```
screen -wipe
```

Splitting windows
-----------------


```
Ctrl-a Shift-s (^a S)
```


```
Ctrl-a TAB (^a TAB)
```

Now that you’re in the new area, there’s not much to do but to create another shell, so do it with:

```
Ctrl-a Ctrl-c (^a ^c)
```

From here you can start another application in the regular way, and give the internal shell a name of its own.

If you want to close the application in the bottom area, do it as normal, then to kill the area, press:

```
Ctrl-a Shift-x (^a X)
```

If you want to kill every area **EXCEPT** then one you’re currently in, you can use:

```
Ctrl-a Shift-q (^a Q)
```

And that’s pretty much it when it comes to **screen**. For more information, check out the command:

```
man screen
```

Good luck!
