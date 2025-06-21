+++
date = '2025-06-21T12:45:39+02:00'
draft = false
title = 'Exposing our application to the internet'
authors = ["Matt Britt"]
tags = ["homelab","k3s","GitOps"]
+++

The linkding app is now running on our cluster, but in order to make it useful it would be good to expose it to the internet.

<!--more-->

It would be better, from a security point of view, not to have to open any ports to the internet.

Cloudflare offers a nice free solution (if you host a domain with them) called Cloudflare tunnels.

They work by opening a connection from the client (application end) to Cloudflare, that stays open and allows traffic to route from Cloudflare to your network - after you setup a CNAME record.

First we need to install and configure the ___cloudflared___ app.

```shell
pacman -S cloudflared
cloudflared tunnel login
```

We then need to create a tunnel named ___ldpi___.

```shell
# will createe a json file /home/matt/.cloudflared
cloudflared tunnel create ldpi
```

We then use the generated file to create a secret in K3S.

```shell
kubectl create secret generic tunnel-credentials \
--from-file=credentials.json=58c97568-9eda-4dbf-9857-b6cf9cf91259.json
```

Using the application id in the json file we need to add a CNAME record to Cloudflare (with proxying)

```shell
# The CNAME needs to point to this record (our cname is links)
58c97568-9eda-4dbf-0857-b6cf9cf91259.cfargotunnel.com
```

We now need to add a kubernetes service that will be configured to point to our deployment.

```yaml
# in service.yaml (base layer of linkding)
# and edit kustomization file!
apiVersion: v1
kind: Service
metadata:
  name: linkding
spec:
  ports:
    - port: 9090
  selector:
    app: linkding
  type: ClusterIP

```

We then create a new deployment that will run the cloudflared image and make the initial call to Cloudflare to setup the tunnel.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cloudflared
spec:
  selector:
    matchLabels:
      app: cloudflared
  replicas: 2 
  template:
    metadata:
      labels:
        app: cloudflared
    spec:
      containers:
      - name: cloudflared
        image: cloudflare/cloudflared:latest
        args:
        - tunnel
        - --config
        - /etc/cloudflared/config/config.yaml
        - run
        livenessProbe:
          httpGet:
            path: /ready
            port: 2000
          failureThreshold: 1
          initialDelaySeconds: 10
          periodSeconds: 10
        volumeMounts:
        - name: config
          mountPath: /etc/cloudflared/config
          readOnly: true
        - name: creds
          mountPath: /etc/cloudflared/creds
          readOnly: true
      volumes:
      - name: creds
        secret:
          secretName: tunnel-credentials
      - name: config
        configMap:
          name: cloudflared
          items:
          - key: config.yaml
            path: config.yaml
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: cloudflared
data:
  config.yaml: |
    tunnel: ldpi

    credentials-file: /etc/cloudflared/creds/credentials.json

    ingress:
    - hostname: links.mattbritt.com
      service: http://linkding:9090
```

When we push these changes to our repo, Flux will provision and setup the tunnel, we are live!

[https://links.mattbritt.com](https://links.mattbritt.com)
