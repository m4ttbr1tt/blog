+++
draft = false
date = 2025-07-09T17:35:51+02:00
title = "LFCS - Operations Deployment - Processes"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
series = ["LFCS"]
+++

Managing Linux processes is critical for DevOps and Linux administration!

<!--more-->

Here are some common commands to manage systemd services/unit files and system processes.

#### Startup Process and Services

```shell
# systemd is an init system 
# service units (tells init system about )
man systemd.service

# look at unit file
systemctl cat ssh.service

sudo systemctl edit --full ssh.service # to edit the service unit
sudo systemctl revert ssh.service # set back 

sudo systemctl status ssh.service
sudo systemctl stop ssh.service
sudo systemctl start ssh.service # start now
sudo systemctl restart ssh.service # after editing config ()
sudo systemctl reload ssh.service # more graceful for users 
sudo systemctl reload-or-restart ssh.service # tries to reload  
sudo systemctl disabled ssh.service
sudo systemctl is-enabled ssh.service
sudo systemctl enable ssh.service # auto start
sudo systemctl enable --now ssh.service # enable and start now
sudo systemctl mask atd.service # prevent a service being started by another service
sudo systemctl unmask atd.service 
sudo systemctl list-units --type service --all # all systemd units
```

#### Systemd unit file

```shell
# eg starting a custom app
man systemd.service
man systemd.unit # for unit part of file
man systemd.exec
man systemd.kill

# look for Restart= option in man page and EXAMPLES

ls /lib/systemd/system

sudo cp /lib/systemd/system/ssh.service /etc/systemd/system/myservice.service

vim myservice.service

sudo systemctl daemon-reload

sudo systemctl start myservice.service
sudo journalctl -f # system log
```

#### Diagnose and Manage Processes

```shell
ps -a # unix syntax
ps a # bsd syntax (not equivalent)
ps # current terminal
ps aux  # ax - all, u - user   (reminder "aux"illary)
man ps # EXAMPLES
# kernel processes wrapped in []

top   # constantly reorders processes
ps 1 # pid
ps u 1 # user oriented format
ps -U matt #  for a specific user
ps -U u matt #  for a specific user
pgrep -a syslog  # process grep with name

nice -n 11 bash # assigns priority
ps lax # shows niceness
ps fax # forest all (tree)
ps faux # with user info 
nice -n -12 bash #permission denied (lower nice value)
renice 7 1238 # pid id
# only root can lower niceness

# signals
kill -L
systemctl status ssh.service
kill -SIGKILL 23434 # pid

# all processes with name containing bash

pgrep - a bash # check first

pkill -KILL bash # kill all bash

sleep 180

CTRL-Z # puts app in background
fg # gets paused app back

sleep 300 & # backgrounding a process

jobs # checks background processes

fg 1  # id
bg # background again

lsof -p 13536 # what files or dirs is process using

sudo lsof /some/path # nothing is using file if not result
```
