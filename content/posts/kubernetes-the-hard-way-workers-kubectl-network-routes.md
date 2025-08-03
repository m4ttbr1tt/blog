+++
draft = false
date = 2025-07-29T19:51:31+02:00
title = "Kubernetes The Hard Way - Workers, kubectl and Network routes"
authors = ["Matt Britt"]
tags = ["K8S"]
series = ["Kubernetes The Hard Way"]
+++

The final part of this series is to get the workers running and to configure connections to the cluster via kubectl.

<!--more-->

Full notes and scripts are available on my [GitHub](https://github.com/m4ttbr1tt/lab/tree/main/kubernetesthehardway)

### Worker bootstrapping

```shell
#!/bin/bash

# Copy the Kubernetes binaries and systemd unit files to each worker instance:
#

for HOST in node-0 node-1; do
  SUBNET=$(grep ${HOST} machines.txt | cut -d " " -f 4)
  sed "s|SUBNET|$SUBNET|g" \
    configs/10-bridge.conf > 10-bridge.conf

  sed "s|SUBNET|$SUBNET|g" \
    configs/kubelet-config.yaml > kubelet-config.yaml

  scp 10-bridge.conf kubelet-config.yaml \
  root@${HOST}:~/
done


for HOST in node-0 node-1; do
  scp \
    downloads/worker/* \
    downloads/client/kubectl \
    configs/99-loopback.conf \
    configs/containerd-config.toml \
    configs/kube-proxy-config.yaml \
    units/containerd.service \
    units/kubelet.service \
    units/kube-proxy.service \
    root@${HOST}:~/
done


for HOST in node-0 node-1; do
  scp \
    downloads/cni-plugins/* \
    root@${HOST}:~/cni-plugins/
done


# on node-0 and node-1
{
  apt-get update
  apt-get -y install socat conntrack ipset kmod
}

swapon --show
swapoff -a

# Create the installation directories:

mkdir -p \
  /etc/cni/net.d \
  /opt/cni/bin \
  /var/lib/kubelet \
  /var/lib/kube-proxy \
  /var/lib/kubernetes \
  /var/run/kubernetes

# Install the worker binaries:

{
  mv crictl kube-proxy kubelet runc \
    /usr/local/bin/
  mv containerd containerd-shim-runc-v2 containerd-stress /bin/
  mv cni-plugins/* /opt/cni/bin/
}


# Create the bridge network configuration file:
#

mv 10-bridge.conf 99-loopback.conf /etc/cni/net.d/


# To ensure network traffic crossing the CNI bridge network is processed by iptables, 
# load and configure the br-netfilter kernel module:

{
  modprobe br-netfilter
  echo "br-netfilter" >> /etc/modules-load.d/modules.conf
}


{
  echo "net.bridge.bridge-nf-call-iptables = 1" \
    >> /etc/sysctl.d/kubernetes.conf
  echo "net.bridge.bridge-nf-call-ip6tables = 1" \
    >> /etc/sysctl.d/kubernetes.conf
  sysctl -p /etc/sysctl.d/kubernetes.conf
}

# Configure containerd
# Install the containerd configuration files:

{
  mkdir -p /etc/containerd/
  mv containerd-config.toml /etc/containerd/config.toml
  mv containerd.service /etc/systemd/system/
}

# Configure the Kubelet
# Create the kubelet-config.yaml configuration file:

{
  mv kubelet-config.yaml /var/lib/kubelet/
  mv kubelet.service /etc/systemd/system/
}


#Configure the Kubernetes Proxy

{
  mv kube-proxy-config.yaml /var/lib/kube-proxy/
  mv kube-proxy.service /etc/systemd/system/
}


# Start the Worker Services

{
  systemctl daemon-reload
  systemctl enable containerd kubelet kube-proxy
  systemctl start containerd kubelet kube-proxy
}

systemctl is-active kubelet
```

### Configure kubectl

```shell
{
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://server.kubernetes.local:6443

  kubectl config set-credentials admin \
    --client-certificate=admin.crt \
    --client-key=admin.key

  kubectl config set-context kubernetes-the-hard-way \
    --cluster=kubernetes-the-hard-way \
    --user=admin

  kubectl config use-context kubernetes-the-hard-way
}
```

### Pod network routes

```shell
{
  SERVER_IP=$(grep server machines.txt | cut -d " " -f 1)
  NODE_0_IP=$(grep node-0 machines.txt | cut -d " " -f 1)
  NODE_0_SUBNET=$(grep node-0 machines.txt | cut -d " " -f 4)
  NODE_1_IP=$(grep node-1 machines.txt | cut -d " " -f 1)
  NODE_1_SUBNET=$(grep node-1 machines.txt | cut -d " " -f 4)
}

ssh root@server <<EOF
  ip route add ${NODE_0_SUBNET} via ${NODE_0_IP}
  ip route add ${NODE_1_SUBNET} via ${NODE_1_IP}
EOF

ssh root@node-0 <<EOF
  ip route add ${NODE_1_SUBNET} via ${NODE_1_IP}
EOF

ssh root@node-1 <<EOF
  ip route add ${NODE_0_SUBNET} via ${NODE_0_IP}
EOF
```

### Final tests

We are ready to test the cluster, verifying that we can create a secret, deployment and service.

```shell
# Create a generic secret:
kubectl create secret generic kubernetes-the-hard-way \
  --from-literal="mykey=mydata"

ssh root@server \
    'etcdctl get /registry/secrets/default/kubernetes-the-hard-way | hexdump -C'

# Create a deployment for the nginx web server:
kubectl create deployment nginx \
  --image=nginx:latest

# List the pod created by the nginx deployment:
kubectl get pods -l app=nginx

# Retrieve the full name of the nginx pod:
POD_NAME=$(kubectl get pods -l app=nginx \
  -o jsonpath="{.items[0].metadata.name}")

# Forward port 8080 on your local machine to port 80 of the nginx pod:
kubectl port-forward $POD_NAME 8080:80

curl --head http://127.0.0.1:8080

# exec commands in container
kubectl exec -ti $POD_NAME -- nginx -v

# Expose the nginx deployment using a NodePort service:
kubectl expose deployment nginx \
  --port 80 --type NodePort

# Retrieve the node port assigned to the nginx service:
NODE_PORT=$(kubectl get svc nginx \
  --output=jsonpath='{range .spec.ports[0]}{.nodePort}')

# Retrieve the hostname of the node running the nginx pod:
NODE_NAME=$(kubectl get pods \
  -l app=nginx \
  -o jsonpath="{.items[0].spec.nodeName}")
```

### Final thoughts

Working through this series has given valuable insight into the underlying workings of Kubernetes. The scripts to get this running are so well written and documented that it was relatively easy to get it running.
The value of working through this comes from further digging into what is being setup and how it works. Always be curious!

Now that I've got the cluster running... I'm going to delete it 🤣 - and install a Talos Linux cluster.
