+++
draft = false
date = 2025-07-21T15:41:31+02:00
title = "Kubernetes The Hard Way - Preparing machines with Ansible"
authors = ["Matt Britt"]
tags = ["K8S"]
series = ["Kubernetes The Hard Way"]
+++


In preparation for working through the "Kubernetes the Hard Way" series, I need to configure my 4 host machines... with Ansible.

<!--more-->

I'm planning on working through the series by Kelsey Hightower, as I'd like a deep dive into the workings of Kubernetes. I have 5 machines that I have installed a K3S cluster onto already, so I need to clean that up before proceeding. [https://github.com/kelseyhightower/kubernetes-the-hard-way](https://github.com/kelseyhightower/kubernetes-the-hard-way)

I thought it would be a good idea to do this with Ansible, as it will save a lot of manual effort.

Ansible is an open-source automation tool used for system configuration management, it is agentless, which means there is nothing to install on the machines you are managing, everything is managed through ssh. [https://docs.ansible.com/](https://docs.ansible.com/)

I really love its declarative nature, reminds me of using Nix for my system, Im going to setup config of my main machine with Ansible.

We need an inventory of the machines we are managing:

```ini
# inventory.ini
[all]
node-1 ansible_host=192.168.1.51
node-2 ansible_host=192.168.1.52
node-3 ansible_host=192.168.1.53
node-4 ansible_host=192.168.1.54

[all:vars]
ansible_user=root
ansible_ssh_private_key_file=~/.ssh/ansible-key
```

And then a playbook to execute. This is a simple playbook that will manage the uninstall of K3S from the 4 managed machines:

```shell
# ssh-keygen -t ed25519 -f ~/.ssh/ansible-key -C "ansible key"
# Copy to each target node
#
# for host in node-0 node-1 server jump; do
#   ssh-copy-id -i ~/.ssh/ansible-key.pub root@$host
# done
#
#
# ansible-playbook -i inventory.ini k3s.yml --tags uninstall-server
# ansible-playbook -i inventory.ini k3s.yml --tags uninstall-agents

- name: Manage k3s application
  hosts: all
  become: true
  tasks:
    - name: Uninstall K3s Server
      when: inventory_hostname in ['node-1', 'node-2','node-3']
      shell: /usr/local/bin/k3s-uninstall.sh
      args:
        removes: /usr/local/bin/k3s
      tags: uninstall-server
    - name: Uninstall K3s Agent
      when: inventory_hostname in ['node-4']
      shell: /usr/local/bin/k3s-agent-uninstall.sh
      args:
        removes: /usr/local/bin/k3s
      tags: uninstall-agents
```

Now we can quickly uninstall K3S with one command from the host machine. 😍
