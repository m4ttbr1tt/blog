+++
date = '2025-06-28T11:15:39+02:00'
draft = false
title = 'Ingress with Traefix'
authors = ["Matt Britt"]
tags = ["homelab","k3s","GitOps"]
+++
In the last post we port forwarded to the Grafana service, but that kinda sucks.

<!--more-->

I'm going to setup an ingress using the helm chart.

First we determine the config for the helm chart by showing its values.

```shell
# need to add repo to helm
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm show values prometheus-community/kube-prometheus-stack
```

And then adding the necessary config to release.yaml values section.

```yaml
ingress:
 enabled: true
```

Force Flux reconcile...

```
flux reconcile kustomization monitoring-controllers --with-source
```

Setup local dns...

```shell
sudo vim /etc/hosts
192.168.10.60 grafana.mattbritt.com 
```

We will now have internal network access to our dashboard!

[https://github.com/m4ttbr1tt/homelab](https://github.com/m4ttbr1tt/homelab)
