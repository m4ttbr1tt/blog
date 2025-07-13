+++
draft = false
date = 2025-07-10T17:35:51+02:00
title = "LFCS - Operations Deployment - Log Files"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
series = ["LFCS"]
+++

Log files give you insight into your system and are very important for SRE and DevOps.

<!--more-->

Here are some of the most commonly used commands for managing and viewing system log files:

```shell
# logging daemons
# /var/log 
# rsyslog  - rocket-fast system for log processing
su --login

grep -r 'ssh' /var/log

less /var/log/syslog
# numbered

#live view of log file

tail -F /var/log/auth.log # follow mode

# Systemd Journal daemon
which sudo

journalctl /user/bin/sudo # search logs for an app
journalctl -u ssh.service # logs generate by ssh.service unit
journalctl -e # end
journalctl -f # follow

# info warning err crit

journalctl -p err
journalctl -p info -g '^b' # grep
journalctl -S 01:00 # since 
journalctl -S 01:00 -U 02:00 # since until

journalctl -b 0 # boot zero
journalctl -b -1 # 1 boot ago

sudo mkdir /var/log/journal # will log historical boots

last # last logins
lastlog # each user last login
```
