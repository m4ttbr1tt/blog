+++
date = '2025-06-10T17:45:39+02:00'
draft = false
title = 'Homelab high availability K3S cluster install'
description = "Finished the installation of the HA K3S cluster!"
authors = ["Matt Britt"]
tags = ["homelab","k3s"]
+++

I've completed the install of a high availability K3S cluster onto the 5 Optiplex machines. Had one or two issues, that meant I had to reinstall with a new secret, but otherwise was pretty smooth!

<!--more-->

| Node  | IP           | Role          | Notes            |
| ----- | ------------ | ------------- | ---------------- |
| molly | 192.168.1.51 | control plane | --cluster-init   |
| daisy | 192.168.1.52 | control plane | joins molly      |
| rosie | 192.168.1.53 | control plane | joins molly      |
| bessy | 192.168.1.54 | worker        | joins any server |
| elsie | 192.168.1.55 | worker        | joins any server |

### Secret for all machines

You need to supply the __same__ secret to each machine! The initially installed control plane and other control planes or agent nodes need the same secret to join the cluster.

```shell
echo "somerandomsecret" > secret
```

### Install the initial control plane

```shell
curl -sfL https://get.k3s.io | K3S_TOKEN=$(cat secret) sh -s - server \
    --cluster-init \
    --disable=helm-controller

sudo systemctl status k3s.service
```

### Additional server installs

```shell
curl -sfL https://get.k3s.io | K3S_TOKEN=$(cat secret) sh -s - server \
    --server https://192.168.1.51:6443 \
    --disable=helm-controller
```

### Agent install

```shell
curl -sfL https://get.k3s.io | K3S_TOKEN=$(cat secret) sh -s - agent --server https://192.168.1.51:6443
```

### Connecting to cluster

```shell
# remote control plane

sudo cp /etc/rancher/k3s/k3s.yaml .

# On main system

scp matt@192.168.1.51:/home/matt/k3s.yaml .

vim k3s.yaml # edit IP to point to control plane

mkdir ~/.kube

mv k3s.yaml .kube/config
```

![cluster](/posts/homelab-high-availability-k3s-cluster/cluster.png)

### Uninstalling

In case you need to uninstall..

```shell
# servers
/usr/local/bin/k3s-uninstall.sh
# agents
/usr/local/bin/k3s-agent-uninstall.sh
```

Done! 😍

Next will be setting up Flux for GitOps!
