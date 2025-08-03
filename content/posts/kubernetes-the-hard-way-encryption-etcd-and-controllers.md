+++
draft = false
date = 2025-07-27T11:31:31+02:00
title = "Kubernetes The Hard Way - Encryption, Etcd and Controllers"
authors = ["Matt Britt"]
tags = ["K8S"]
series = ["Kubernetes The Hard Way"]
+++


Continuing with the series, next is to get encryption setup and to bootstrap the etcd datastore and cluster controllers.

<!--more-->

Full notes and scripts are available on my [GitHub](https://github.com/m4ttbr1tt/lab/tree/main/kubernetesthehardway)

### Encryption

```shell
# Generate encryption Key
#
export ENCRYPTION_KEY=$(head -c 32 /dev/urandom | base64)

# Create the encryption-config.yaml encryption config file:
# envsubst is a shell command that replaces environment variables in a file
#
envsubst < configs/encryption-config.yaml \
  > encryption-config.yaml


# Copy the encryption-config.yaml encryption config file to each controller instance:

scp encryption-config.yaml root@server:~/

```

### etcd datastore

etcd is a high availability key value datastore that Kubernetes uses for all its data.

After we have copied the binaries and created the systemd unit files we can start the services:

```shell
# Start the etcd Server
#
{
  systemctl daemon-reload
  systemctl enable etcd
  systemctl start etcd
}


# List the etcd cluster members:
#
etcdctl member list
```

### Bootstrapping controllers

Controllers manage the desired state of the cluster, they monitor the current state and make requests to adjust the state if required.

```shell
# Install the Kubernetes binaries:
#
{
  mv kube-apiserver \
    kube-controller-manager \
    kube-scheduler kubectl \
    /usr/local/bin/
}
```

After creating config files in the correct locations, we can start the services...

```shell
# Start the Controller Services

{
  systemctl daemon-reload

  systemctl enable kube-apiserver \
    kube-controller-manager kube-scheduler

  systemctl start kube-apiserver \
    kube-controller-manager kube-scheduler
}
```

And then we can verify all is good!

```shell
kubectl cluster-info \
  --kubeconfig admin.kubeconfig
```
