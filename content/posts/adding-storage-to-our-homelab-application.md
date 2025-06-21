+++
date = '2025-06-18T18:45:39+02:00'
draft = false
title = 'Adding storage to our homelab application'
authors = ["Matt Britt"]
tags = ["homelab","k3s","GitOps"]
+++

In order to persist any data that will be generated be linkding, we need to make sure that we have persistent storage.

<!--more-->

First we need to provision a PVC.

```yaml
#base/linkding/pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: linkding-pv-claim
  namespace: linkding
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

And then alter our deployment to add the volume and mount it to the container.

```yaml
#base/linkding/deployment.yaml
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
          volumeMounts:
            - name: linkding-data
              mountPath: "/etc/linkding/data"
      volumes:
        - name: linkding-data
          persistentVolumeClaim:
            claimName: linkding-data-pvc
```

With Flux running in our cluster all we need to do is to commit and push our changes to the homelab repo! 💥
