---
title: Testing connectivity without netcat
date: 2025-06-12
authors:
    - jorge
categories:
    - Linux
tags:
    - connectivity
    - netcat
    - nc
    - bash
    - python
    - linux
---

I encountered a server that did not have **netcat** or **telnet** installed, and I needed to check the connectivity against a server on a certain port. How did I solve this? There are multiple options!

<!-- more -->

If you have **netcat** installed:

```bash
nc -zv example.com 443
```

With **bash**:

```bash
timeout 3 bash -c '</dev/tcp/example.com/443' && echo "Port is open" || echo "Port is closed"
```

With **python**:

```python
python3 -c "import socket; s=socket.socket(); s.settimeout(3); s.connect(('example.com', 443))" && echo "Port is open" || echo "Port is closed"
```
