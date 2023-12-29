---
title: Gnome notification area gone?
date: 2008-08-02
authors:
    - jorge
categories:
    - Linux
---
Right, this problem has been annoying me for ages. Sometimes the Gnome Notification area will disappear, leaving applications such as Pidgin or NetworkManager lingering outside the system tray area. Very annoying. The solution?

If you are using just one monitor, open the file named *%gconf.xml* located in *~/.gconf/apps/panel/general/* and add the following:

```

notificationarea_screen0

```

This should be added between the starting and closing *entry*-tags. Save and close the file, followed up by restarting your X-session. Things should be back to normal.

Now, if you’re using two monitors, with a separate X-screen on each, this will most likely happen to you. The reason behind this is that you must have only one notification area, one for both screens.

To solve this, remove the upper panel on both screens. Re-add the panel on your main monitor as you like it, and add a notification area there. Re-add it on your second monitor as well, only this time don’t add the notification area. Give it a shot, let me know if it works. :)