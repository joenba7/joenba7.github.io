---
title: Nifty firewall
date: 2008-11-27
authors:
    - jorge
categories:
    - Linux
---
```
#!/bin/bash

IPTABLES='/sbin/iptables'

# Clear out any existing firewall rules, and any chains that might have
# been created. Then set the default policies.

$IPTABLES -F
$IPTABLES -F INPUT
$IPTABLES -F OUTPUT
$IPTABLES -F FORWARD
$IPTABLES -F -t mangle
$IPTABLES -F -t nat
$IPTABLES -X
$IPTABLES -P INPUT DROP
$IPTABLES -P OUTPUT ACCEPT
$IPTABLES -P FORWARD ACCEPT

# Begin setting up the rulesets. First define some rule chains to handle
# exception conditions. These chains will receive packets that we aren't
# willing to pass. Limiters on logging are used so as to not to swamp the
# firewall in a DOS scenario.
#
# silent       - Just dop the packet
# tcpflags     - Log packets with bad flags, most likely an attack
# firewalled   - Log packets that that we refuse, possibly from an attack

$IPTABLES -N silent
$IPTABLES -A silent -j DROP

$IPTABLES -N tcpflags
$IPTABLES -A tcpflags -m limit --limit 15/minute -j LOG --log-level 4 --log-prefix '** TCP-Flags ** -- '
$IPTABLES -A tcpflags -j DROP

$IPTABLES -N firewalled
$IPTABLES -A firewalled -m limit --limit 15/minute -j LOG --log-level 4 --log-prefix '** Firewalled ** -- '
$IPTABLES -A firewalled -j DROP

# These are all TCP flag combinations that should never, ever, occur in the
# wild. All of these are illegal combinations that are used to attack a box
# in various ways.

$IPTABLES -A INPUT -p tcp --tcp-flags ALL FIN,URG,PSH -j tcpflags
$IPTABLES -A INPUT -p tcp --tcp-flags ALL ALL -j tcpflags
$IPTABLES -A INPUT -p tcp --tcp-flags ALL SYN,RST,ACK,FIN,URG -j tcpflags
$IPTABLES -A INPUT -p tcp --tcp-flags ALL NONE -j tcpflags
$IPTABLES -A INPUT -p tcp --tcp-flags SYN,RST SYN,RST -j tcpflags
$IPTABLES -A INPUT -p tcp --tcp-flags SYN,FIN SYN,FIN -j tcpflags

# Allow selected ICMP types and drop the rest.

$IPTABLES -A INPUT -p icmp --icmp-type 0 -j ACCEPT
$IPTABLES -A INPUT -p icmp --icmp-type 3 -j ACCEPT
$IPTABLES -A INPUT -p icmp --icmp-type 11 -j ACCEPT
$IPTABLES -A INPUT -p icmp --icmp-type 8 -m limit --limit 1/second -j ACCEPT
$IPTABLES -A INPUT -p icmp -j firewalled

# The loopback interface is inheritly trustworthy. Don't disable it or
# a number of things will break.

$IPTABLES -A INPUT -i lo -j ACCEPT

# Now allow Internet hosts access to those services we provide. Note that
# enabling inbound FTP 20 & 21 tcp will also require allowing ports
# 1024-65534/tcp. Which in itself is good enough reason not to allow FTP
# connections and to only allow ssh/scp/sftp.

## SSH

# localhost
$IPTABLES -A INPUT -p tcp --dport 22 -s 127.0.0.1 -j ACCEPT

# example.com
$IPTABLES -A INPUT -p tcp --dport 22 -s 208.77.188.166 -j ACCEPT

# oidentd
$IPTABLES -A INPUT -p tcp --dport 113 -j ACCEPT

## HTTP access from anywhere

$IPTABLES -A INPUT -p tcp -s 0/0 --dport 80 -j ACCEPT

# Allow packets that are part of an established connection to pass
# through the firewall. This is required for normal Internet activity
# by inside clients.

$IPTABLES -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT

# Anything not already matched gets firewalled and logged.

$IPTABLES -A INPUT -j firewalled
```