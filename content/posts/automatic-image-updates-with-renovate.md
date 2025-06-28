+++
date = '2025-06-30T18:15:39+02:00'
draft = false
title = 'Automatic image updates with Renovate'
authors = ["Matt Britt"]
tags = ["homelab","k3s","GitOps"]
+++
If we update our container registry with a new image, or a 3rd party app in our cluster needs updating... that's something to automate!

<!--more-->

We will be using Renovate to automate the pulling in of new images. It will create a merge request in GitHub, and once we approve the MR, the image will be installed into our cluster!

[https://github.com/renovatebot/renovate](https://github.com/renovatebot/renovate)

Our project structure will look like this:

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
├── infrastructure
│   └── controllers
│        ├── base
│             └── renovate 
│        └── staging
│             └── renovate
```

First make a new GitHub classic token with repo access, export it and  store in a password manager.

```shell
export RENOVATE_TOKEN=ghp_123456789abcdefghijklmnopq
```

```
kubectl create secret generic renovate-container-env \
--from-literal=RENOVATE_TOKEN=ghp_123456789abcdefghijklmnopq \
--dry-run=client \
-o yaml > renovate-container-env.yaml
```

Move renovate-container-env.yaml to base/renovate, and encrypt with sops.

```
sops --age=$AGE_PUBLIC \
--encrypt --encrypted-regex '^(data|stringData)$' --in-place renovate-container-env.yaml
```

Setup your other manifests:

```yaml
# infra/controllers/base/renovate/namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: renovate
```

```yaml
# infra/controllers/base/renovate/cronjob.yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: renovate
  namespace: renovate
spec:
  schedule: "@hourly"
  concurrencyPolicy: Forbid
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: renovate
              image: renovate/renovate:latest
              args:
                - m4ttbr1tt/homelab 

              envFrom:
                - secretRef:
                    name: renovate-container-env
                - configMapRef:
                    name: renovate-configmap

          restartPolicy: Never

```

```yaml
# infra/controllers/base/renovate/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: renovate-configmap
  namespace: renovate
data:
  RENOVATE_AUTODISCOVER: "false"
  RENOVATE_GIT_AUTHOR: "Renovate Bot <bot@renovateapp.com>"
  RENOVATE_PLATFORM: "github"

```

Add this renovate.json into your homelab git root:

```json in root of repo
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "kubernetes": {
    "fileMatch": [
      "\\.yaml$"
    ]
  }
}
```

Push and reconcile with flux.

[https://github.com/m4ttbr1tt/homelab](https://github.com/m4ttbr1tt/homelab)
