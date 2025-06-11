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

When I first used Kubernetes in production in 2018, we did not have GitOps. The deployments and manifests were written in bash scripts and it was difficult to maintain, there was often drift or uncertainty as to the state of the cluster was in. GitOps is a modern way to resolve this, and its beautiful 😍.

### Which GitOps tool to choose?

There are two main tools in this space Flux and Argo

![pods](/posts/homelab-gitops-with-flux/pods.png)
