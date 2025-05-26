+++
draft = false
date = '2025-05-26T19:39:56+02:00'
title = 'Bluefin and dotfiles management changes'
description = "Update on Bluefin and dotfiles changes"
slug = "bluefin-and-dotfiles-management-changes"
authors = ["Matt Britt"]
tags = ["devpod", "bluefin"]
+++

Last week I decided to ditch NixOS, as it was getting in the way of my work. I've setup a Fedora based distribution called Bluefin, which went smoothly but has taken some getting used to. There are things that have been great, and some not so much, mainly due to the desktop environment, which I'll need to resolve.

I've setup a dotfiles management solution that is the best I've had. Credit to Mischa and Rio. It is highly portable across bare-metal and containers.

### Chezmoi - dot files manager

Its a dotfiles manager that manages the state of your .config directory (it does diffs and applies and state changes), across multiple machines.

You can read more about it here <https://www.chezmoi.io/>

### Mise - a development environment setup tool

With the  *mise.toml*  file you specify the dependencies you would like to install for a specific project, it has template support, secret management with sops and age, and more.

You can read more about it here <https://mise.jdx.dev/>

### Devpod

I have integrated these tools with devcontainers to build a declarative, reproducible config environment.

Using devpods (<https://devpod.sh/>) and the hooks it provides for executing scripts, I've combined  these three tools into something that I am very happy with.

All credit to Mischa and Rio in the KubeCraft community. Thank you so much.

### Hyprland on Arch

I'm not enjoying the desktop experience on Bluefin. Having worked entirely in a window manager for the last few years, just having tmux as a sudo WM is not enough, I need to be able to be virtually 100% keyboard based, so I'm busy figuring out next steps. Likely an Arch & Hyprland iteration, with the aim of a setup that is  **declarative and reproducible**.

![devpods](/posts/bluefin-and-dotfiles-management-changes/devpods.png)
