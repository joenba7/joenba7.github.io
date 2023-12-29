---
title: vim &#8211; Goodbye to :set paste
date: 2017-07-17
authors:
    - jorge
categories:
    - Linux
tags:
    - 'bracketed paste mode'
    - 'disable set paste'
    - linux
    - 'set paste'
    - vim
---
I’ve been using **vim** as my editor of choice ever since I started learning Linux, and something that has been bothering me for a while is how **vim** handles pasting.

<!-- more -->

```
:set paste
```

When everything is pasted, I turn it off with:

```
:set paste!
```

or:

```
:set nopaste
```

The command **:set paste** prevents **vim** from auto-indenting the code I’ve just pasted.

Luckily, as it most often goes, there is a solution. Why I haven’t bothered to actually find the answer till recently is a whole other matter.

As it turns out, my terminal of choice (which currently is **rxvt-unicode**) supports something called **bracketed paste mode**.

In short, when **bracketed paste mode** is set, pasted text is bracketed with control sequences so that the program can differentiate between pasted text and typed-in text.

Let’s stay that I copied the text:

```
Hello World!
```

from another program. When I paste it into my terminal, if it supports **bracketed paste mode**, it actually sends the text:

```
\e[200~Hello World!\e[201~
```

Now the thing is to let **vim** know how to watch out for these control sequences, and tell it what to do. Paste the following code into your **.vimrc**:

```
let &t_SI .= "\[?2004h"
let &t_EI .= "\[?2004l"

inoremap   [200~ XTermPasteBegin()

function! XTermPasteBegin()
set pastetoggle=[201~
set paste
return ""
endfunction
```

And that should reduce your use of **:set paste** quite a bit!
