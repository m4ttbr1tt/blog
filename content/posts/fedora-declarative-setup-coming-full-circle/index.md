+++
draft = false
date = 2026-06-06T21:50:00+02:00
title = 'Fedora and declarative setup: coming full circle'
description = "Moving from NixOS to Bluefin, then landing on Fedora with a custom declarative setup."
slug = "fedora-declarative-setup-coming-full-circle"
authors = ["Matt Britt"]
tags = ["Fedora", "NixOS", "Bluefin", "dotfiles"]
+++

After moving from [NixOS to Bluefin](/posts/18-months-on-nixos-and-im-done-moving-to-bluefin/) and then tweaking my [dotfiles and dev environment](/posts/bluefin-and-dotfiles-management-changes/), I have ended up somewhere that feels obvious in hindsight: plain Fedora, with my own declarative setup layered on top.

<!--more-->

<!-- Image placeholder: add Fedora / desktop setup screenshot here later. -->

NixOS gave me the thing I wanted: a reproducible machine, tracked in git, with a clear source of truth. I still like that idea. The problem was the cost. Too often I was debugging the system instead of doing the work I actually cared about. When I needed a normal Linux escape hatch, it usually came with another Nix-shaped rabbit hole.

Bluefin fixed a lot of that. It was Fedora underneath, container friendly, and much closer to the DevOps workflows I use day to day. But I still wanted more control over the desktop, the package set, and how my machine bootstraps. I did not want another clever OS layer between me and the thing I was trying to configure.

So I have come full circle.

## The current setup

I am now running Fedora with my dotfiles repo as the source of truth:

```bash
git clone git@github.com:m4ttbr1tt/dotfiles.git
cd dotfiles
./setup
```

The `setup` script is intentionally boring. That is the point.

It checks that the system is Fedora-based, upgrades the machine, enables the repos I need, installs my base packages, sets up Rust, npm tools, fonts, Flatpaks, Zed, fish, chezmoi, and my Niri/DMS desktop bits.

There are also explicit commands for the heavier optional stuff:

```bash
./setup packages
./setup clonerepos
```

That gives me a clean split between "make this machine mine" and "pull down all my bigger apps and personal repos".

## Declarative enough

This is not NixOS. I am not pretending it is.

But it is declarative enough for what I actually need:

- packages are listed in arrays
- dotfiles are managed with chezmoi
- repos are cloned consistently
- desktop services are enabled in one place
- setup is repeatable on a fresh Fedora install

The difference is that when something does not fit the model, I can just use Fedora normally. RPMs, Flatpaks, COPRs, shell scripts, systemd units. No drama.

## The lesson

I still value reproducibility. I just do not want reproducibility at the expense of momentum.

Fedora plus a boring setup script gives me most of what I liked about NixOS, without the restrictions that made me leave it. It also gives me the flexibility I liked in Bluefin, without being tied to an opinionated image.

For now, this feels like the sweet spot: declarative where it helps, normal Linux where it matters.
