+++ 
draft = false
date = 2025-05-17T17:21:51+02:00
title = "Installing K3S and K9S on an Ubuntu Server VM"
description = "A summary of the install process for K3S and K9S on a Ubuntu Server VM"
slug = "installing-k3s-and-k9s-on-a-ubuntu-server-vm"
authors = ["Matt Britt"]
tags = ["K9S", "K3S"]
categories = []
externalLink = ""
series = []
+++


K3S is a lightweight version of Kubernetes and is used for running on resource constrained environments.

###### To install run the following

```shell
curl -sfL https://get.k3s.io | sh - 
```

 There is an interesting output to note after the install, K3S installs a single binary and creates 3 symlinks.

```shell
[INFO]  Installing k3s to /usr/local/bin/k3s
[INFO]  Creating /usr/local/bin/kubectl symlink to k3s
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Creating /usr/local/bin/ctr symlink to k3s
```

###### Check that the cluster is running

```shell
sudo k3s kubectl get node
```

![k3s install](/installing-k3s-k9s-on-a-vm/k3s.png)

### K9S is a CLI application that manages your Kubernetes cluster

Ive install K9S onto my NixOS system (which is beyond the scope of this summary)

###### To start K9S run the following

```shell
k9s --kubeconfig /etc/rancher/k3s/k3s.yaml
```

K9S showing currently running pods on the newly installed K3S cluster:

![k9s cli application](/installing-k3s-k9s-on-a-vm/k9s.png)

