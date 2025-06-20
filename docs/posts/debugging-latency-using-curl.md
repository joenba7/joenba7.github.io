---
title: Debugging latency using curl
date: 2024-02-21
comments: true
authors:
    - jorge
categories:
    - Linux
tags:
    - curl
    - latency
    - ssl
---

# Debugging latency using curl

I had an issue recently where I had to debug why a request to a website was
taking such a long time. After a bit of searching around, I found the following
bit of code:

<!-- more -->

```bash
curl -w "dns_resolution: %{time_namelookup}, tcp_established: %{time_connect}, ssl_handshake_done: %{time_appconnect}, TTFB: %{time_starttransfer}\n" -o /dev/null -s "https://jorge.fbarr.net/"
```

The output of the command is as follows:

```
dns_resolution: 0.044536, tcp_established: 0.047217, ssl_handshake_done: 0.081556, TTFB: 0.216986
```

* dns_resolution == Time to resolve DNS
* tcp_established == Time to establish TCP connection
* ssl_handshake_done == Time of SSL handshake
* TTFB == Time To First Byte

The times are measured in seconds (ssl handshake - tcp time).

Request the same URL twice to see the result of Time To First Byte without an
SSL handshake:

```bash
curl -w "dns_resolution: %{time_namelookup}, tcp_established: %{time_connect}, ssl_handshake_done: %{time_appconnect}, TTFB: %{time_starttransfer}\n" -o /dev/null -s "https://jorge.fbarr.net/" "https://jorge.fbarr.net/"
dns_resolution: 1.525, tcp_established: 1.630, ssl_handshake_done: 2.027, TTFB: 2.136
dns_resolution: 0.000, tcp_established: 0.000, ssl_handshake_done: 0.000, TTFB: 0.110
```

A neat trick to have up your sleeve when debugging!
