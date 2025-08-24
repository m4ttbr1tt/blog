+++
draft = false
date = 2025-08-15T19:11:31+02:00
title = 'Talos Linux Synology NAS storage'
authors = ["Matt Britt"]
tags = ["homelab","k8s","Talos", "GitOps"]
+++

Next step in the Talos Linux cluster setup is to get storage provisioning working on my Synology NAS!

<!--more-->

First step was to create a new Synology user on the NAS, and then to setup the manifests in the homelab repo.

### Synology CSI Talos Driver & configs

There is a repo that you need to clone in order to get the driver installed onto the cluster.

```shell
git clone https://github.com/zebernst/synology-csi-talos.git
```

After editing the config we create a secret with the NAS connection and user details.

```shell
 # create secret 
 kubectl create secret -n synology-csi generic client-info-secret 
--from-file=../synology-csi-talos/config/client-info-template.yml
```

We build a docker image from the make file, first setting up a GitHub auth token so that docker can push and pull from the container registry.

```shell
export GITHUB_TOKEN=2234234234234234 # Token from GitHub with package read and write permissions
echo $GITHUB_TOKEN | docker login ghcr.io -u m4ttbr1tt --password-stdin

# from synology-csi-talos repo
make docker-build
```

We also need to create a secret that will be used from within the cluster in order to pull the image and provision the pods

```shell
kubectl create secret docker-registry ghcr-login-secret --docker-server=https://ghcr.io
--docker-username=m4ttbr1tt 
--docker-password=PASS_WITH_WRITE_AND_READ_PACKAGES --docker-email=mb@mattybritt.com -n synology-csi
```

### Troubleshooting pod failures

The install had issues, for some reason the iscsi-tools extension had not been installed correctly. I had created a Talos Linux ISO including the extension but when I checked the nodes it was not installed.

Checking a Talos Linux node for installed binaries:

```shell
# The extension was not listed!
talosctl ls -r /usr/local -n 192.168.1.51
```

I regenerated an image (including siderolabs/iscsi-tools) and upgraded each node, pointing to the provided url:

```shell
# for each node
talosctl upgrade --image factory.talos.dev/metal-installer/c9078f9419961640c712a8bf2bb9174933dfcf1da383fd8ea2b7dc21493f8bac:v1.10.6 -n 192.168.1.51
```

The pvc for the linkding data then appeared on the Synology NAS!

![synology](/posts/talos-linux-synology-storage/synology.png)

With the shared storage now running we can start to deploy some useful apps! Nice! 💥

[https://github.com/m4ttbr1tt/homelab](https://github.com/m4ttbr1tt/homelab)
