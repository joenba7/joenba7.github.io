---
title: Introduction to irssi
date: 2010-08-10
authors:
    - jorge
categories:
    - Linux
---
There are a load of IRC clients for UNIX-based systems out there, but I personally feel that **irssi** suits me best. Incredibly flexible, adjustable for every user, and doesn’t depend on X to be able to run it. Haven’t really found a guide out there that explains things in an easy and understandable manner, aimed particularly at new users. So here’s my attempt at it.

Getting started
---------------

What we need, of course, is **irssi** itself. You can get it at http://www.irssi.org/download, or use your system’s package manager to download it.

```
yum install irssi
```

..for RPM-based systems.

```
sudo aptitude install irssi
```

..for Debian/Ubuntu type of distributions.

Now, once installed, you can fire up **irssi** by typing

```
irssi
```

..in the commandline. You should now be running **irssi**, whoo!

Customizing the settings
------------------------

Now that **irssi** is up and going, you can change the settings, adjust it so that if suits you. By typing:

```
/SET 
```

you will get a list of settings. For instance, let’s try out **/SET nick**. This is what pops up:

```
 [completion]
 completion_nicks_lowercase = OFF
 [lookandfeel]
 hilight_nick_matches = ON
 query_track_nick_changes = ON
 show_nickmode = ON
 show_nickmode_empty = ON
 show_own_nickchange_once = OFF
 [misc]
 netjoin_max_nicks = 10
 netsplit_max_nicks = 10
 netsplit_nicks_hide_threshold = 15
 [server]
 alternate_nick = joenba7_
 nick = joenba7
```

Here you see the various settings containing the word nick. **irssi** will automatically set your nick based on your account name. So if your username on your machine is **foo**, **irssi** will set your nick to **foo**. You can of course change this:

```
/SET nick joenba7
/SET alternate_nick joenba7_
```

..followed up by a **/SAVE** and **/RELOAD** to save and reload the settings.

**Note**: Every time you change a default setting, you will have to use **/SAVE** to save the configuration.

Connecting to an IRC-network
----------------------------


```
/SERVER powertech.no.eu.dal.net
```

This will then connect me to the server **powertech.no.eu.dal.net** on the DALnet network. At the same time though, I’d like to be on the **EFnet** network. You can connect to multiple networks by typing:

```
/CONNECT irc.homelien.no
```

This connects you to the **EFnet** network as well. You are perhaps wondering how you can change from one network to the other? Look at your status-window. This is usually window number 1. Use **ALT-1** or **ESC-1** to change to that window. Right now my active network is **EFnet**, on the server **irc.homelien.no**. Which means that I’m seeing this:

```
[08:38] [joenba7(+i)] [1:homelien (change with ^X)]
```

**«Change with ^X»** – As you’ve probably guessed, **^X** means **Ctrl-X**, so upon pressing it, you will change your active network. You will see something like this in your status window:

```
08:38 -!- Irssi: Changed to server powertech.no.eu.dal.net
```

To disconnect from one of the servers, or stop **irssi**’s attempt at reconnecting, use:

- **/DISCONNECT**  – Disconnects server with tag network.
- **/DISCONNECT recon-1** – Cancels an attempt to connect to the server named recon-1.
- **/RMRECONNS** – Cancels all attempts to reconnect.
- **/RECONNECT** recon-1 – Will attempt to reconnect you to recon-1.
- **/RECONNECT ALL** – Will attempt to reconnect to all the servers in the reconnection queue.

Navigation
----------

You can scroll upwards/downwards with the **Page Up** / **Page Down** keys. If they aren’t working for you, you can use **Meta-p** / **Meta-n**, where **Meta** can be **ESC** and/or **ALT**.

Default behavior is that **irssi** pretty much creates hidden windows for just about everything. Hidden windows are created every time you join a channel or start a conversation. There are different ways to switch between them:

- **Meta-1 .. Meta-0** – Switch between window 1-10.
- **Meta-q .. Meta-o** – Switch between window 11-19.
- **/WINDOW** – Switch to window number .
- **Ctrl-p** / **Ctrl-n** – Switch to the previous/next window.

Adding networks, servers and channels
-------------------------------------

On the network **DALnet** you have a function called **Nickserv**, which lets you register a nick, so that you can become the owner of it. To be able to use this nick you will have to identify to it when logging on to the IRC network. It’s a bit tiresome having to type **/nickserv identify** every time you connect. I’m assuming you want this a bit more automated? I at least do. :) But before we do this we have to do a couple of things. Like adding a network:

**Note:** use **/IRCNET** with version 0.8.9 and older.

```
/NETWORK ADD 
```

For example:

```
/NETWORK ADD DALnet
```

To remove the network, use:

```
/NETWORK REMOVE 
```

If you’d like to execute a command when logging on, like identifying to your nick, use **-autosendcmd**:

```
/NETWORK ADD -autosendcmd "/nickserv identify  ;wait -DALnet 2000" DALnet
```

The command above automatically sends the **identify**-command to **Nickserv** when you log on. The **-wait 2000** tells **irssi** to wait two seconds after having logged on to the server before sending the command.

Now that we’ve added a network, we need servers to go along with it. The command to do so is:

```
/SERVER ADD -network DALnet powertech.no.eu.dal.net 6667
```

If you’d like to automatically connect to this server when you start **irssi**, simply add an **-auto** right after **ADD**. Specifying the port is optional.

So, first network, then server, now for a channel:

```
/CHANNEL ADD -auto #linuxhjelp DALnet [password]
```

**\[password\]** is optional, use of it depends on if the channel you’re trying to join needs a password to be accessed.

Logging
-------

**irssi** has the ability to log both away-messages and regular conversations in channels. Logging for away messages is enabled by default, but not regular logging. First, it’s possible to define what the away-log should log:

- **/SET awaylog\_level MSGS HILIGHT** – Defines what kind of messages to log.
- **/SET awaylog\_file ~/.irssi/away.log** – Defines the file the log should write to.

The easiest way of enabling logging, and not just away messages, is to execute:

```
/SET autolog ON
```

Per default **irssi** logs to **~/irclogs/something.log**. This can be defined by:

```
/SET autolog_path ~/irclogs/$tag/$0.log
```

That is the default value. The path will automatically be created if it doesn’t exist. **$0** points to the target (channel/nick). You can make **irssi** to automatically rotate the logs by adding a time/date stamp to the filename. You find more about the formatting used by typing **man strftime** in your commandline. Example:

```
/SET autolog_path ~/irclogs/%Y/$tag/$0.%m-%d.log
```

**%Y** for year, **%m** for month and **%d** for day.

For more information about logging have a look at:

```
/HELP LOG
```

Scripts
-------

If you’ve used other IRC clients, you’ve probably encountered the possibility to use scripts. Scripts can be used for various things, from showing a channel what song you’re currently playing to flaunting your machine’s hardware specs. A good site for scripts is http://scripts.irssi.org.

The scripts you download should be placed in **~/.irssi/scripts/**. If the folder doesn’t exist, create it yourself. Once the script is placed there, you can load it in your **irssi** by doing a:

```
/SCRIPT LOAD ~/.irssi/scripts/TheScript.pl
```

This is a good approach if you don’t want certain scripts to load when **irssi** starts. If you do however want them to load when starting **irssi**, create a **symlink** in the folder **~/.irssi/scripts/autorun**, just create the folder if it doesn’t exist. **symlink**, what’s that? Well, it’s a special type of file that contains a reference to another file or directory in the form of an absolute or relative path. So in other words:

```
cd ~/
wget http://scripts.irssi.org/scripts/splitlong.pl
mkdir -p ~/.irssi/scripts/autorun
mv ~/splitlong.pl ~/.irssi/scripts
cd ~/.irssi/scripts/autorun
ln -s ../splitlong.pl
```

So the file is really in **~/.irssi/scripts**, but you’ve made a shortcut to it from **~/.irssi/scripts/autorun**. Pretty neat if you ask me.

To unload a script simply use:

```
/SCRIPT UNLOAD 
```

Minor adjustments
-----------------

It’s not just what I’ve mentioned above that you can adjust and customize to your own liking. Another thing is the use of themes. You’re probably using the default blue theme that **irssi** comes with, which is just fine for my use, but perhaps not for you. I do however like the minor modification just for kicks.

Every time you use **/SAVE,** a message like this will appear in your status window:

```
08:38 -!- Irssi: Saved configuration to file /home/jorge/.irssi/config
08:38 -!- Irssi: Theme saved to /home/jorge/.irssi/default.theme
```

Settings are stored in the configuration file named **config** and the theme in **default.theme**. Both files are editable through a text editor. I’ve never really changed things manually in the config-file, but I’ve tampered with **default.theme** a bit, like what my own nick appears to me. Default appearance of your nick is like so:

```

```

Where **YourNick** is in bold white, while the inequality signs are in gray.

Open the file **~/.irssi/default.theme** and find the line that says something like:

```
ownmsgnick = "{msgnick $0 $1-}";
```

Change this to:

```
ownmsgnick = "%R%n %|";
```

This will make the inequality signs turn red. Your nick will still be in white bold. But you can always change the inequality signs to something else. For instance:

```
ownmsgnick = "%Y(%n$0$1-%Y)%n %|";
```

Will make the parenthesis turn yellow. If you want to change the color of your nick, from default white to something else, find this line:

```
ownnick = "%W$*%n";
```

..and replace it with the color you want:

```
ownnick = "%G$*%n";
```

For a nice overview of colors, check out http://irssi.org/documentation/formats, and for more information about **irssi**, visit http://www.irssi.org/.
