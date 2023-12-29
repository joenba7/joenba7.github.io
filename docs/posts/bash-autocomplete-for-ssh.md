---
title: BASH autocomplete for SSH
date: 2012-04-18
authors:
    - jorge
categories:
    - Linux
---
If you, like me, find yourself SSH’ing all day long, this little piece of code will do wonders. Well, it’ll save you some keystrokes at least. This line parses your **.bash\_history**-file and tries to autocomplete the host you are trying to SSH to, given that you’ve already used SSH to access the host in question at least once before.

So, to make this work, place the following in your **.bashrc** or **.bash\_profile** file:

```
complete -W "$(echo $(grep '^ssh ' .bash_history | sort -u | sed 's/^ssh //'))" ssh
```


```
source .bashrc
```

..to apply the change made to your **.bashrc**, if that’s the file you’ve made the change to. You should now be able to autocomplete hosts when SSH’ing with TAB.

**Update**: I am also (now) aware of, thanks to my colleagues, that you have to tweak and sometimes install additional packages to have a similar functionality provided by your system’s package manager. I find the solution above the easiest one, as it’s only a single line that needs to be included in a file, without any additional tweaks or packages necessary. But in the end, it’s your call!