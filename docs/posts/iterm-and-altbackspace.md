---
title: iTerm and alt+backspace
date: 2018-06-08
authors:
    - jorge
categories:
    - Apple
tags:
    - alt
    - backspace
    - iTerm2
    - macOS
    - terminal
---
I recently changed jobs, going from a Linux environment to macOS. When I worked in the terminal in Linux, I got used to using **alt+backspace** to delete entire words. Unfortunately, it seems that this function isn’t native to the **Terminal** application that macOS has.However, in the application [iTerm2](https://www.iterm2.com/) it’s possible to add your own key-combinations so that **alt+backspace**, or in this case **cmd+backspace**, works as it does in Linux.

Just follow these instructions:

- Open up **iTerm2** and head on over to **Preferences**
- Go to **Keys** -&gt; **Key Mappings** and press the **+**.
- In the **Action** field, select **Send Hex Code**.
- In the field **Keyboard Shortcut**, just press **cmd+backspace**.
- In the field **Hex code** enter **0x1B 0x08**.

And that’s it! **cmd+backspace** in **iTerm2** should now have the same functionality as **alt+backspace** in Linux!

