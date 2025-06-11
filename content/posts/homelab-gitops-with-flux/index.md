+++
date = '2025-06-11T16:45:39+02:00'
draft = false
title = 'GitOps with Flux'
description = "Ive setup Flux on the homelab cluster to start our GitOps cluster managment"
authors = ["Matt Britt"]
tags = ["homelab","k3s","GitOps"]
+++


Today I installed Flux onto the cluster in order to implement GitOps for my homelab.

<!--more-->

### What is GitOps?

GitOps is a best practice for code delivery of applications and infrastructure. It uses git as the source of truth and a controller that runs on the cluster, constantly checking the git repo for any changes and reconciling the declared state with the cluster.

> GitOps enables declarative cluster state

When I first used Kubernetes in production in 2018, we did not have GitOps. The deployments and manifests were written in bash scripts and it was difficult to maintain, there was often drift or uncertainty as to the state the cluster was in. GitOps is a modern way to resolve this, and its beautiful 😍.

### Which GitOps tool to choose?

There are two main tools in this space Flux and Argo.

Ive chosen Flux for the following reasons:

- Kustomize is used by default (Application customisation)
- CLI focused (Argo has a great UI, but Id rather not use that)
- Used by default in Azure AKS (which is what I know)

[https://fluxcd.io](https://fluxcd.io)

### Installing Flux

First create a personal token on GitHub and export the token, as well as your GitHub username, as an environment variable.

```shell
export GITHUB_TOKEN=ghp_234234234234234234234
export GITHUB_USER=m4ttbr1tt
```

Next check your cluster meets the prerequisites...

```shell
flux check --pre
```

..and install Flux.

```shell
# will install to cluster and then push manifests to github
flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=homelab \
  --branch=master \
  --path=./clusters/staging \
  --personal
```

You will now see Flux pods on the cluster...
![pods](/posts/homelab-gitops-with-flux/pods.png)

[https://github.com/m4ttbr1tt/homelab](https://github.com/m4ttbr1tt/homelab)
