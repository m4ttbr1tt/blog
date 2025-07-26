+++
draft = false
date = 2025-07-17T17:35:51+02:00
title = "LFCS - Networking"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
series = ["LFCS"]
+++

A core part of any SysAdmin, SRE or DevOps role! Networking is up next in my LFCS prep...

<!--more-->

I have a long history with networking and the topics below, but its good to brush up on these fundamentals, and to learn new things too!

#### Managing Networking

```shell
#CIDR - Classes Inner Domain Routing
ip link # shows networking devices
ip address # show ip address
ip a -c # colour

sudo ip link set dev enp0s8 up
sudo ip addr add 10.0.0.40/24 dev enp0s8
sudo ip addre delete 10.0.0.40/24 dev enp0s8

sudo netplan get # ubuntu networking tool

sudo cat /ect/netplan/50-cloud-init.yaml

sudo netplan try
sudo netplan try --timeout 30

ip route # routes
resolvectl status # dns resolvers
vim /etc/systemd/resolved.conf
sudo systemctl restart systemd-resolved.service
resolvectl dns

vim /etc/hosts # local ips

man netplan

ls /usr/share/doc/netplan/examples/static.yaml

```

#### Managing Services

```shell
# sshd daemon

sudo ss -ltunp # socket / services util
-l listening
-t tcp
-u udp
-n numeric
-p processes

ss --help

systemctl status ssh.service
sudo systemctl stop ssh.service
sudo systemctl disable ssh.service
sudo systemctl enable ssh.service

ps 679 # process id from ss command
sudo lsof -p 679 # files opened by command

sudo netstat -ltunp # formatted differently
```

#### Configuring Bridge and Bonding Devices

```shell
# virtual network devices
# bridge "bridges" two network cards so machines on either network can talk to each other as if on the same network

# bonding is when two physical network cards are logically merged into one (good for failover) 

# bonding modes
# mode 0 - 6
# 0 - round robin 
# 1 - active backup
# 2 - xor
# 3 - broadcast
# 4 - ieee 802.3ad
# 5 - adaptive transmit load balancing
# 6 - adaptive load balancing
```

```shell
ls /usr/share/doc/netplan/examples/bridge.yaml

cp /usr/share/doc/netplan/examples/bridge.yaml /etc/netplan/99-bridge.yaml
ip -c link
# edit ethernets and interfaces in definitions of 99-bridge.yaml
sudo netplan try

cp /usr/share/doc/netplan/examples/bonding.yaml /etc/netplan/99-bond.yaml
sudo chmod 600 /etc/netplan/99-bond.yaml
# edit yaml to match devices

sudo netplan apply

cat /proc/net/bonding/bond0
```

#### Configuring Firewalls

```shell
# packet filtering
# ufw (uncomplicated firewall)
sudo ufw status

sudo ufw allow 22 # tcp and udp for ssh
sudo ufw allow 22/tcp

sudo ufw enable
sudo ufw states verbose

ss -tn # active tcp connections

sudo ufw allow from 10.0.0.192 to any port 22
sudo ufw status numbered # order list of rules
sudo ufw delete 1 # removes a rule

sudo ufw delete allow 22 # to delete a rule

sudo ufw status

# can be a range
sudo ufw allow from 10.0.0.0/24 to any port 22
sudo ufw allow from 10.0.0.0/24 # any port

# all ips except one ip
sudo ufw deny from 10.0.0.37

sudo ufw status numbered

# reorder deny rule
sudo ufw delete 2

sudo ufw insert 1 deny from 10.0.0.37 # adds as first rule

# apply a rule on an interface
ip link
ping -c 4 8.8.8.8

sudo ufw deny out on enp0s3 to 8.8.8.8 # deny outgoing traffic

ip a
sudo ufw allow in on enp0s3 from 10.0.0.192 to 10.0.0.100 proto tcp
sudo ufw allow out on enp0s3 from 10.0.0.100 to 10.0.0.192 proto tcp
sudo ufw status numbered
```

#### Port Redirection and NAT

```shell
# enable ip forwarding
/etc/sysctl.conf
#or
/etc/systctl.d/99-sysctl.conf
# uncomment relevant lines
sudo sysctl --system
sudo sysctl -a | grep forward # check if set

# port redirection

nft # net filter framework
iptables # predecessor

ip -a # to see ips and subnets
ip -r # routes

# ip table chains

sudo iptables -t nat -A PREROUTING -i inp1s0 -s 10.0.0.0./24 -p tcp --dport 8080 -j DNAT --to-destination 192.168.0.5:80

sudo iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o enp6s0 -j MASQUERADE # masquerade is an extension

sudo nft list ruleset

sudo apt install iptables-persistent # to save rules
sudo netfilter-persistent save

man ufw-framework
sudo iptables --list-rules --table nat
sudo iptables --flush --table nat # clear if you made a mistake


```

#### Reverse Proxy and Load Balancer

```shell
sudo vim /etc/nginx/sites-available/proxy.conf
cat /etc/nginx/proxy_params

/etc/nginx/sites-enabled/proxy.conf # enabled configs

# soft link from available to enabled
sudo ln -s /etc/nginx/sites-available/proxy.conf /etc/nginx/sites-enabled/proxy.conf

sudo nginx -t

# loadbalancer
# nginx can load balance

```

#### Set and Sync System Time

```shell
# NTP servers (network time protocol)

timedatectl list-timezones

sudo timedatectl set-timezone Africa/Johannesburg

timedatectl
sudo apt install systemd-timesyncd # to sync time
sudo timedatectl set-ntp true

systemctl status systemd-timesyncd.service

sudo vim /etc/systemd/timesynd.conf
```

#### Configuring SSH Servers and Clients

```shell
sudo vim /etc/ssh/sshd_config #ssh daemon config
sudo vim /etc/ssh/ssh_config #ssh client config

man sshd_config

sudo systemctl reload ssh.service

ls /etc/ssh/sshd_config.d # check there are no clashing settings

# clients
ls -la ~/.ssh

man ssh_config
vim ~/.ssh/config # edit name so you dont need to remember server name
chmod 600 ~/.ssh/config

# key gen

ssh-keygen
ls ~/.ssh

ssh-copy-id matt@server # copies public keys to server
cat ~/.ssh/authorized_keys # will have public key that is authorised (this is on server)

ssh-keygen -R 10.0.0.251 # remove a known_hosts entry
```

Beautiful! 😍
