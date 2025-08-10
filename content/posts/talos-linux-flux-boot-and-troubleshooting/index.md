+++
draft = false
date = 2025-08-05T20:11:31+02:00
title = 'Talos Linux Flux boot and troubleshooting'
authors = ["Matt Britt"]
tags = ["homelab","k8s","Talos", "GitOps"]
+++


After setting up the cluster I needed to boot it from the Flux repository using our GitOps setup. I ran into a few issues with Talos Linux not being healthy.

<!--more-->

### Flux config on new cluster

As we have our homelab configured for GitOps the setup should be fairly easy.

First check that flux prechecks pass and then bootstrap the cluster for flux. (making sure our GitHub creds are in environment variables)

```shell
flux check --pre

flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=homelab \
  --branch=master \
  --path=./clusters/staging \
  --personal

```

### Setup flux secret

We have existing secrets that have been encrypted with sops, so we need to manually create the secret in order for those secrets to be decrypted successfully.

```shell
# Add the private key to cluster
cat age.agekey |
kubectl create secret generic sops-age \
--namespace=flux-system \
--from-file=age.agekey=/dev/stdin
```

When I attempted to reconcile the flux cluster there were issues, and the cluster was unhealthy.

![health](/posts/talos-linux-flux-boot-and-troubleshooting/health.png)

It turned out that some Talos Linux components where not coming up on one of the machines. I used various commands to check logs, edit settings and finally drained the node and deleted from the cluster.

```shell
# status of control plane nodes
talosctl -n 192.168.1.53 containers --kubernetes  

talosctl -n 192.168.1.53 logs -k  kube-system/kube-scheduler-rosie:kube-scheduler:3bbfaf624942

# debugging startup
talosctl -n 192.168.1.53 logs controller-runtime


# edit time settings
talosctl edit machineconfig -n 192.168.1.53 --mode=reboot

# cordon and delete bad nodes
# cordon and drain
talosctl -n 192.168.1.53 reset 

# delete node
kubectl delete node rosie
```

The dashboard command lets you get access to the running nodes stats, pretty cool.

```shell
# dashboard for a node
talosctl -n 192.168.1.53 dashboard
```

![dashboard](/posts/talos-linux-flux-boot-and-troubleshooting/dashboard.png)

The cluster is still not quite right as I need to get a storage setup on my NAS, so will get that done soon!

[https://github.com/m4ttbr1tt/homelab](https://github.com/m4ttbr1tt/homelab)
