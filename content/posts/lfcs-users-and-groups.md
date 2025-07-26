+++
draft = false
date = 2025-07-14T17:35:51+02:00
title = "LFCS - Users and Groups"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
series = ["LFCS"]
+++

Continuing with LFCS prep, we are moving on to user and group management...

<!--more-->

#### User management

```shell
sudo adduser matt # will be prompted for password and info
# adds user and group matt
# home user is created
# default shell is /bin/bash
# copied from /etc/skel (like a template)
sudo passwd matt
sudo deluser matt
sudo deluser --remove-home matt
sudo adduser --shell /bin/othershell --home /home/otherdir/ matt 

cat /etc/passwd # for user id home and shell id

ls -ln home # will print out user id
id # shows the user and group id
whoami # shows the current username

sudo adduser --system --no-create-home sysacc # system account, intented for progams

sudo usermod --home /home/otherdir --move-home matt # move and change home dir

sudo usermod -d /home/otherdir -m matt # move and change home dir

sudo usermod --login matt bob # change the users name or -l
sudo usermod --shell /bin/othershell matt # or -s

sudo usermod --lock matt # disables account or -L
sudo usermod --unlock matt
sudo usermod --expiredate 2028-12-10 matt # account expiry
sudo chage --lastday 0 matt # change age of password (will force to login next login)
sudo chage --maxdays 30 matt # force change every 30 days
sudo chage --maxdays -1 matt # never expires
sudo chage --list matt
```

#### Local Groups and Group Membership

```shell
# each user can belong to one or more groups
# used to manage permissions
# user has primary / login group
sudo groupadd developers 
sudo gpasswd --add matt developers # gpasswd is short for group password or -a
groups matt # lists all groups that matt is part of
sudo gpasswd --delete matt developors # or -d  remove from group
sudo usermod -g developers matt # sets the primary group
sudo usermod --gid matt matt

sudo groupmod --new-name programmers developers # rename group name or -n
sudo groupdel programmers
```

#### Manage System-Wide Environment Profiles

```shell
printenv # or env
# user specific env vars .bashrc
sudo vim /etc/environment # system wide vars

logout
echo $SYSVAR

# run something everytime any user logs in

sudo vim /etc/profile.d/lastlogin.sh # create file
```

#### Manage Template User Environment

```shell
sudo vim /etc/skel/README # edit or create new files
```

#### Configure User Resource Limits

```shell
sudo /vim/etc/security/limits.conf
# domain type item value
# domain is user or group or *
# type  - hard, soft or -
# item eg. nproc, fsize, cpu
man limits.conf

#limits.conf
matt - nproc 3 #

sudo -iu matt # real login
ps | less # shell and these two are three process
ls -a | grep bash | less # will not run as limited to 3 processes

ulimit -a # see limits
ulimit -u 5000 # lower limit or raise up to hard value
```

#### Manage User Privileges

```shell
groups # show groups for user
# add user to sudo
sudo gpasswd -a matt sudo # add user to sudo

# edit sudoers file
sudo visudo

%sudo ALL=(ALL:ALL) ALL
#group HOST=(RUNASUSER:RUNASGROUP) LISTOFCOMMANDS

matt ALL=(ALL) ALL # no run as group

sudo -u john ls /home/john # runs command as user john

matt ALL=(john,jane) ALL # matt can only run commands as users john or jane

matt ALL=(ALL) /bin/ls, /bin/stat # only certain commands 
matt ALL= /bin/ls, /bin/stat # only certain commands 

```

#### Manage Access to Root

```shell
sudo ls /root # runs command as root
sudo --login # login as root or -i
logout
su - # will ask for root passwd
su -l # will ask for root passwd
su --login # will ask for root passwd

sudo passwd --lock root # sets password based logins
```

#### Configure LDAP user and groups

```shell
# user accounts are store in /etc/passwd
# LDAP server (Lightweight Directory Access Protocol)
# Single server change across multiple servers

id john
# no such user

#lxc  (linux containers)
#lxd init  (managers containers)

lxc import ldap-server.tar.xz
lxc list
lxc start ldap-server

sudo apt install libnss-ldap

cat /etc/nsswitch.conf # name server switch, edited after config (where to find info) eg. files systemd ldap

nslcd # name service local daemon (gets the actual data from the ldap server)
sudo cat /etc/nslcd.conf # config file 

#nsl gets the info
getent passwd --service ldap
getent group --service ldap

sudo pam-auth-update # to auto create user home (pluggable auth modules)
```
