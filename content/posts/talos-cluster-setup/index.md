+++
draft = false
date = 2025-07-31T20:11:31+02:00
title = 'Talos Linux Cluster Setup'
authors = ["Matt Britt"]
tags = ["homelab","k8s","Talos"]
+++


Its now time to get a production grade cluster running with Talos Linux!

<!--more-->

### Talos Linux

Talos Linux runs Kubernetes in a secure, immutable environment, you are not able to connect to the machines with SSH. It is all managed through talosctl (a cli application)

[Talos Linux](https://www.talos.dev/)

I have 5 homelab machines running and on my home network, I'll be installing Talos Linux with the following setup:

| Node  | IP           | Role          |
| ----- | ------------ | ------------- |
| molly | 192.168.1.51 | control plane |
| daisy | 192.168.1.52 | control plane |
| rosie | 192.168.1.53 | control plane |
| bessy | 192.168.1.54 | worker        |
| elsie | 192.168.1.55 | worker        |

Talos Linux has a unique (and very cool 😍) way of getting setup!

- Boot the machine from a USB (the OS is loaded into RAM)
- Remove the USB drive (so you don't inadvertently install onto it)
- Configure network and host options (optional)
- Connect via talosctl from another machine (I'm using my main box)
- Init and configure the cluster, control plane and worker nodes

### Custom Image download

I'll be setting up a Synology NAS as an external storage device, which requires the image to include the iscsi-tools extension. You can download a custom image from the below site.

[https://factory.talos.dev/](https://factory.talos.dev/)

![image-factory](/posts/talos-cluster-setup/imagefactory.png)

### Cluster initialisation

Burn the image to a USB drive and boot up the first control plane box. Once the machine is booted, unplug the USB drive! You can configure a static ip and then from the host machine setup the node, and initialise the cluster.

```shell
export CONTROL_PLANE_IP=192.168.1.51   
```

```shell
talosctl get disks --insecure --nodes $CONTROL_PLANE_IP
```

![disks](/posts/talos-cluster-setup/disks.png)

```shell
export CLUSTER_NAME=cowctl
export DISK_NAME=sdb
```

```shell
# generate configs 
talosctl gen config $CLUSTER_NAME https://$CONTROL_PLANE_IP:6443 --install-disk /dev/$DISK_NAME
```

```shell
# applies configs
talosctl apply-config --insecure --nodes $CONTROL_PLANE_IP --file controlplane.yaml
```

```shell
# sets endpoints in config file
talosctl --talosconfig=./talosconfig config endpoints $CONTROL_PLANE_IP
```

```shell
# bootsrap etcd (once per cluster)
talosctl bootstrap --nodes $CONTROL_PLANE_IP --talosconfig=./talosconfig
```

```shell
# merge configs
talosctl kubeconfig --nodes $CONTROL_PLANE_IP --talosconfig=./talosconfig
```

```shell
# check cluster health
talosctl --nodes $CONTROL_PLANE_IP --talosconfig=./talosconfig health
```

### Additional control plane or worker nodes

```shell
# additional control planes or workers
# you might need to check the drive if it differs from machine to machine

talosctl get disks --insecure --nodes [NODE IP]
export DISK_NAME=sda

talosctl apply-config --insecure \
    --nodes 192.168.1.52 \
    --file controlplane.yaml
#or 
talosctl apply-config --insecure \
    --nodes 192.168.1.52 \
    --file worker.yaml
```

Nice we have our cluster up and running!

```shell
kubectl get nodes -o wide

```

![nodes](/posts/talos-cluster-setup/talosnodes.png)

I plan to run my homelab with this setup for the foreseeable future! But things change, so lets see what happens 😅.

[https://github.com/m4ttbr1tt/homelab](https://github.com/m4ttbr1tt/homelab)
