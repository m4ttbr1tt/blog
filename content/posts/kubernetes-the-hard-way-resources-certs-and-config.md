+++
draft = false
date = 2025-07-24T18:21:31+02:00
title = "Kubernetes The Hard Way - Resources, Certs and Config"
authors = ["Matt Britt"]
tags = ["K8S"]
series = ["Kubernetes The Hard Way"]
+++


This week I have been working through the Kubernetes The Hard Way series. I setup resources and configured certs and kubeconfigs for infrastructure.

<!--more-->

Full notes and scripts are available on my [GitHub](https://github.com/m4ttbr1tt/lab/tree/main/kubernetesthehardway)

### Resources config

With the machines configured in a txt file I prepped them for ssh access first, and verified the connection.

```shell
# copy public key to each machine
#
while read IP FQDN HOST SUBNET; do
  ssh-copy-id root@${IP}
done < machines.txt
```

```shell
# verify connection
#
while read IP FQDN HOST SUBNET; do
  ssh -n root@${IP} hostname
done < machines.txt


```

I configured a hostname access from the jumpbox:

```shell
while read IP FQDN HOST SUBNET; do
  ENTRY="${IP} ${FQDN} ${HOST}"
  echo $ENTRY >> hosts
done < machines.txt

# append to machine hosts file
cat hosts >> /etc/hosts

#chech ssh using host names
for host in server node-0 node-1
  do ssh root@${host} hostname
done


# add /etc/host entries to remotes
while read IP FQDN HOST SUBNET; do
  scp hosts root@${HOST}:~/
  ssh -n \
    root@${HOST} "cat hosts >> /etc/hosts"
done < machines.txt

```

### Cert configuration

In order for the Kubernetes components to have secure access to each other, you need to setup TLS and discribute the certs to the machines.

#### Control Plane

- kube-apiserver - core component server (exposes k8s http api)
- kube-controller-manager - runs controller processes (manages state of api resources)
- kube-scheduler - watches for new pods and assigns to node

#### Nodes

- kubelet - agent on each node - makes sure containers are running in pods
- kube-proxy - network proxy running on each node

Once all the certificates are generated then we need to copy them to the nodes:

```shell
# Copy the certificates and private keys to the node-0 and node-1
#
for host in node-0 node-1; do
  ssh root@${host} mkdir /var/lib/kubelet/

  scp ca.crt root@${host}:/var/lib/kubelet/

  scp ${host}.crt \
    root@${host}:/var/lib/kubelet/kubelet.crt

  scp ${host}.key \
    root@${host}:/var/lib/kubelet/kubelet.key
done

```

### Kubeconfig generation

Config files need to be generated and copied to the server and workers. For example:

```shell
# Generate a kubeconfig file for the kube-controller-manager service:
#
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://server.kubernetes.local:6443 \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config set-credentials system:kube-controller-manager \
    --client-certificate=kube-controller-manager.crt \
    --client-key=kube-controller-manager.key \
    --embed-certs=true \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-controller-manager \
    --kubeconfig=kube-controller-manager.kubeconfig

  kubectl config use-context default \
    --kubeconfig=kube-controller-manager.kubeconfig
}

```

```shell
# Copy the kube-controller-manager and kube-scheduler kubeconfig files to the server machine:
#
scp admin.kubeconfig \
  kube-controller-manager.kubeconfig \
  kube-scheduler.kubeconfig \
  root@server:~/

```

Nice. Honestly, the instructions on the Kubernetes the Hard Way are very well written, the learning comes from diving into each command and asking why? and why again? Until you get to the first principal of the concept you are dealing with. Never take things for granted always ask questions and dig deeper.
