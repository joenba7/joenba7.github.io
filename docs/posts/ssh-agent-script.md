---
title: ssh-agent-script
date: 2008-08-14
authors:
    - jorge
categories:
    - Linux
    - Snippets
---
To be placed in your **.bashrc**-file:

```
SSHAGENT="/usr/bin/ssh-agent"
SSHAGENTARGS="-s"

if [ -z "$SSH_AUTH_SOCK" -a -x "$SSHAGENT" ]; then
  eval `$SSHAGENT $SSHAGENTARGS`
    trap "kill $SSH_AGENT_PID" 0
fi
```