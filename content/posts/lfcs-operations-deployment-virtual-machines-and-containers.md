+++
draft = false
date = 2025-07-12T17:35:51+02:00
title = "LFCS - Operations Deployment - Virtual Machines and Containers"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
series = ["LFCS"]
+++

Understanding VMs and containers, and how to manage them in Linux, is an important skill to master.

<!--more-->

Here is a list of commands that are useful for managing docker containers and QEMU-KVM VMs.

#### Create and Manage Containers

```shell
docker images # may get a permission denied if not in correct user group
docker search nginx
docker pull nginx
docker pull ubuntu/nginx

docker images # list
docker rmi ubuntu/nginx # remove images
docker run nginx # starts a container, will be inside container

docker run --detach --publish 8080:80 --name mycontainer nginx  
docker ps # lists running containers
docker ps --all # all

docker start 2342342343 # container id
docker stop container_name

nc localhost 8080 # connects to port 8080 with netcat

docker rm container_name # removes container

# order of cleanup
docker stop mycontainer
docker rm mycontainer
docker rmi nginx

# container to always restart
docker run --detach --publish 8080:80 --name mywebserver nginx --restart always

# create an image with Dockerfile

FROM nginx
COPY index.html /user/share/nginx/html/index.html

docker build --tag mattb/customnginx:1.0 mydir
```

#### Manage Virtual Machines

```shell
# QEMU-KVM
# Quick Emulator - Kernel-based Virtual Machine
# virsh - manage virtual machines from the command line

sudo apt install virt-manager # installs other cli utils

# create an xml config (see online)

virsh define testmachine.xml
virsh help
virsh list --all
virsh start TestMachine
virsh list
virsh reboot TestMachine
virsh reset TestMachine # force reset
virsh shutdown TestMachine # graceful shutdown
virsh destroy TestMachine # hard power off (does not destroy)
virsh undefine TestMachine # will remove machine defined
virsh autostart TestMachine # automstart when server boots
virsh dominfo TestMachine # shows specs
virsh set # tab tab

# set cpus
virsh help setvcpus
virsh setvcpus TestMachine 2 --config
virsh setvcpus TestMachine 2 --config --maximum

# set memory
virsh setmaxmem TestMachine 2048M --config
virsh setmem TestMachine 2048M --config

# download ubuntu image
wget https://images......./release/ubuntu.img

qemu-img info ubuntu.img
qemu-img resize ubuntu.img 10G # changes the size of the virtual disk

# add to default storage pools
sudo cp ubuntu.img /var/lib/libvirt/images/

virt-install

virt-install --osinfo list # lists types of os's
man virt-install

# disk images has os preinstalled so we can just import not install!

virt-install --help
virt-install --osinfo ubuntu24.04 --name ubuntu1 --memory 3072 --vcpus 1 --import --disk /var/lib/libvirt/images/ubuntu.img --graphics none

sudo apt install -y libguestfs-tools # setting root password
virsh shutdown ubuntu1
sudo virt-customize -a /vir/lib/libvirt/images/ubuntu.img --root-password password:somepassword123

virsh start ubuntu1
virsh console ubuntu1 # reattach to console

sudo apt install libosinfo-bin # check for latest os support

osinfo-query os # what os are available for the tool


# install a vm (without having a cloud image file) rather from an iso

virt-install --osinfo debian12 --name debian1 --momory 1024 --vcpus 1 --disk size=10  --location /var/lib/libvirt/boot/debian.iso --graphics none --extra-args "console=ttyS0" # serial port

# or you can use a url instead of iso
virt-install --osinfo debian12 --name debian1 --momory 1024 --vcpus 1 --disk size=10  --location https://deb.debian.org/debian/dists/bookworm/main/installer-amd64/  --graphics none --extra-args "console=ttyS0" # serial port
```
