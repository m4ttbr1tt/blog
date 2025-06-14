+++
date = '2025-06-14T16:45:39+02:00'
draft = false
title = 'Installing our first homelab application with GitOps'
authors = ["Matt Britt"]
tags = ["homelab","k3s","GitOps"]
+++


After setting up Flux on the cluster, its time to put it to use! I'll be deploying an app called Linkding, which is a bookmark manager.

<!--more-->

[https://linkding.link/](https://linkding.link)

### Ways to structure your git repo

Flux has some recommendations on how to structure your git repo. For my homelab I'll be using the below structure.

```shell
├── apps
│   ├── base
│   ├── production 
│   └── staging
├── infrastructure
│   ├── base
│   ├── production 
│   └── staging
└── clusters
    ├── production
    └── staging
```

For details and alternatives you can see more details here...

[https://fluxcd.io/flux/guides/repository-structure](https://fluxcd.io/flux/guides/repository-structure)

### Kustomization file

Flux has a kustomization file that reads in other kustomize files (its the entry point)

Any Kustomization file in the clusters > staging dir will be included.

### Flux app config file

Add this file to clusters > staging  (it is configured to look in the ./apps/staging path that we are setting up in the next step)

```yaml
# apps.yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: apps
  namespace: flux-system
spec:
  interval: 1m
  retryInterval: 1m
  timeout: 1m
  sourceRef:
    kind: GitRepository
    name: flux-system
  path: ./apps/staging   # references the dir just setup
  prune: true
```

### Base layers

Shared config is stored in base layers and then overridden in derived layers with a patches definition.

Eg.

```shell
├── apps
│   ├── base
│   ├── production 
│   └── staging
```

### Adding an app Kustomization file

```shell
mkdir -p apps/staging/linkding
mkdir -p apps/base/linkding
```

```shell
cd apps/staging/linkding
touch kustomization.yaml
```

```yaml
# kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: linkding
resources:
 - ../.../base/linkding # reads in base config directory
```

```shell
cd apps/base/linkding
touch kustomization.yaml
touch namespace.yaml
touch deployment.yaml
```

```yaml
# kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
 - namespace.yaml
 - deployment.yaml
```

```yaml
# namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
 name: linkding
```

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: linkding
spec:
  replicas: 1
  selector:
    matchLabels:
      app: linkding
  template:
    metadata:
      labels:
        app: linkding
    spec:
      containers:
        - name: linkding
          image: sissbruecker/linkding:1.31.0
          ports:
            - containerPort: 9090
```

Once pushed to git you can check the status by running the below command. Once the cluster state has been reconciled, you will see the state change (or you will receive a notification of any failures)

```shell
flux get kustomizations
```

![flux](/posts/installing-our-first-homelab-application-with-gitops/flux.png)

And then port forward to check that the app is running.

```shell
kubectl port-forward linkding-pod 8080:9090 #8080 is localport 9090 is the port exposed by deployment
```

We will be setting up a service to manage this soon. Until next time 😍

[https://github.com/m4ttbr1tt/homelab](https://github.com/m4ttbr1tt/homelab)
