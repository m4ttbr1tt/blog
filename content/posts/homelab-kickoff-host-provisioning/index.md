+++
date = '2025-06-07T20:45:39+02:00'
draft = false
title = 'Homelab kickoff - host provisioning'
description = "Installation of 5 Dell Optiplex mini-pcs for the homelab."
authors = ["Matt Britt"]
tags = ["homelab","k3s"]
+++

To start the homelab I've completed the installation of Ubuntu Server onto the 5 Dell Optiplex mini-pcs that will be running my Kubernetes homelab.<!--more-->

I will be running a K3S cluster initially and then upgrading to use Talos Linux at a later stage. Ill be using GitOps to track the state of the cluster, so the migration to Talos should be easy.I setup the machines with the below network config (static ip addresses)

```shell
vim /etc/netplan/99_config.yaml
```

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      addresses:
        - 192.168.1.51/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
          addresses: [1.1.1.1]

```

### Machine Names

The initial laptop that I ran a test cluster on was one of my daughters, and it had cow stickers all over it, so for better or worse here are my machines and their ips.

```shell
molly 192.168.1.51
daisy 192.168.1.52
rosie 192.168.1.53
bessy 192.168.1.54
elsie 192.168.1.55
```

I then applied my changes...

```shell
sudo netplan apply
```

...and tested ssh connectivity from my main machine. (I still need to setup dns)

```shell
ssh matt@192.168.1.51

```

We are ready to get the cluster installed and running!

I crimped some Cat5e connectors too! Not done that in a while 😀

![cabinet](/posts/homelab-kickoff-host-provisioning/cabinet.jpeg)
