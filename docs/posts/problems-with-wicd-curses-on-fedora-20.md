---
title: Problems with wicd-curses on Fedora 20
date: 2014-01-27
authors:
    - jorge
categories:
    - Linux
tags:
    - fedora
    - 'fedora 20'
    - 'python error'
    - wicd
    - wicd-curses
---
If you’re on Fedora 20, are trying to run **wicd-curses**, and keep getting errors like the one below:

```
Traceback (most recent call last):
  File "/usr/share/wicd/curses/wicd-curses.py", line 1067, in 
    main()
  File "/usr/share/wicd/curses/wicd-curses.py", line 995, in main
    ui.run_wrapper(run)
  File "/usr/lib64/python2.7/site-packages/urwid/raw_display.py", line 241, in run_wrapper
    return fn()
  File "/usr/share/wicd/curses/wicd-curses.py", line 88, in wrapper
    return func(*args, **kargs)
  File "/usr/share/wicd/curses/wicd-curses.py", line 1003, in run
    app = appGUI()
  File "/usr/share/wicd/curses/wicd-curses.py", line 548, in __init__
    self.wiredCB = urwid.Filler(WiredComboBox(wiredL))
  File "/usr/share/wicd/curses/wicd-curses.py", line 378, in __init__
    self.__super.__init__(use_enter=False)
  File "/usr/share/wicd/curses/curses_misc.py", line 351, in __init__
    self.focus = focus
AttributeError: can't set attribute
```

..then the fix is simple. Open up the file **/usr/share/wicd/curses/curses\_misc.py**, go to line **360**, and paste the following **below**:

```
    @property
    def focus(self):
        return self._focus

    @focus.setter
    def focus(self, index):
        self._focus = index

    @focus.deleter
    def focus(self):
        del self._focus

    def set_list(self,list):
        self.list = list
```

**wicd-curses** should be working again. This is currently a bug on Bugzilla ([https://bugzilla.redhat.com/show\_bug.cgi?id=894646](https://bugzilla.redhat.com/show_bug.cgi?id=894646)).
