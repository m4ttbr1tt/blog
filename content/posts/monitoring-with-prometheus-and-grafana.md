+++
date = '2025-06-27T19:15:39+02:00'
draft = false
title = 'Monitoring with Prometheus & Grafana'
authors = ["Matt Britt"]
tags = ["homelab","k3s","GitOps"]
+++

Visibility and monitoring of our homelab will be done by installing a helm chart called kube-prometheus-stack.

<!--more-->

Prometheus collects and stores metrics, Grafana turns them into insights and fancy dashboards.

Our repo will end up with the following directory structure:

```shell
├── apps
│   ├── base
│   ├── production 
│   └── staging
├── monitoring
│   ├── config
│   └── controllers
│        ├── base
│             └── kube-prometheus-stack
│        └── staging
│             └── kube-prometheus-stack
```

Here are the manifests (under monitoring/controllers) that we need to get this running:

```yaml
# /base/k-p-s/namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: monitoring
```

```yaml
# /base/k-p-s/repository.yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: HelmRepository
metadata:
  name: kube-prometheus-stack
  namespace: monitoring
spec:
  interval: 24h
  url: https://prometheus-community.github.io/helm-charts
```

```yaml
# /base/k-p-s/release.yaml
apiVersion: helm.toolkit.fluxcd.io/v2
kind: HelmRelease
metadata:
  name: kube-prometheus-stack
  namespace: monitoring
spec:
  interval: 30m
  chart:
    spec:
      chart: kube-prometheus-stack
      version: "66.2.2"
      sourceRef:
        kind: HelmRepository
        name: kube-prometheus-stack
        namespace: monitoring
      interval: 12h
  install:
    crds: Create
  upgrade:
    crds: CreateReplace
  driftDetection:
    mode: enabled
    ignore:
      # Ignore "validated" annotation which is not inserted during install
      - paths: ["/metadata/annotations/prometheus-operator-validated"]
        target:
          kind: PrometheusRule
  values:
    grafana:
      adminPassword: somepassword # todo change

```

```yaml
# /base/k-p-s/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - namespace.yaml
  - repository.yaml
  - release.yaml

```

```yaml
# /staging/k-p-s/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: monitoring
resources:
  - ../../base/kube-prometheus-stack/ 

```

Next under clusters/staging copy the apps.yaml and edit to the following:

```yaml
# clusters/staging/monitoring.yaml

apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: monitoring-controllers
  namespace: flux-system
spec:
  interval: 1m
  retryInterval: 1m
  timeout: 1m
  sourceRef:
    kind: GitRepository
    name: flux-system
  path: ./monitoring/controllers/staging
  prune: true
```

```yaml
# /staging/k-p-s/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - kube-prometheus-stack 

```

Push to git and flux will reconcile and deploy the resources.

Now if we port forward to the service we will have access to our Grafana dashboard:

```shell
kubectl -n monitoring port-forward svc/kube-prometheus-stack-grafana 8080:80
```

[https://github.com/m4ttbr1tt/homelab](https://github.com/m4ttbr1tt/homelab)
