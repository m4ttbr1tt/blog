# AGENT.md

## Repository overview

This repository is a Hugo static site for Matt Britt's personal blog, **"Software Engineering & DevOps - Matt Britt"**. It is configured by `hugo.toml` and uses the vendored Hugo theme at `themes/default` (`Rusty Typewriter`). The blog is primarily about homelab infrastructure, Kubernetes/K3s/Talos, GitOps, Linux, DevOps/SRE practices, and software engineering.

## Key paths

- `hugo.toml` - main Hugo configuration: site title/header, theme, pagination, syntax highlighting, taxonomies, menus, social links, search output, and Rusty Typewriter theme params.
- `content/` - **primary authoring area** for pages and posts. See the detailed content notes below.
- `archetypes/default.md` - default front matter used by `hugo new`; currently creates draft TOML-front-matter pages.
- `data/rtwt/content.yaml` - Rusty Typewriter content snippets, including homepage tagline text.
- `static/` - assets copied directly to the generated site. `static/images/avatar.png` is referenced by `params.avatarurl`.
- `themes/default/` - vendored Rusty Typewriter theme. Prefer site-level content/config/layout overrides over editing this directory.
- `public/` - generated Hugo output. Do not edit by hand.
- `resources/` - Hugo generated resources/cache. Do not edit by hand.

## Development commands

Run from the repository root:

```bash
hugo server
```

Start the local dev server with live reload.

```bash
hugo server -D
```

Start the local dev server including draft content.

```bash
hugo
```

Build the static site into `public/`.

## `content/` structure and conventions

The `content/` directory is the most important part of this repo. Most changes requested by users will likely be edits or additions here.

### Top-level content

- `content/about/index.md` - About/start-here page. Uses `type = 'blank'`, `indexable = true`, and `featured = true`.
- `content/search.md` - Search page. Uses `type = 'blank'`, `indexable = false`, and the `{{< search >}}` shortcode.
- `content/posts/` - Blog posts.

### Blog post forms

Posts use two forms:

1. Single-file posts:

```text
content/posts/post-slug.md
```

2. Page bundles with local images:

```text
content/posts/post-slug/
  index.md
  image.png
```

Use page bundles when a post has colocated images or other post-specific assets.

### Front matter

Content uses TOML front matter delimited with `+++`. Common fields are:

```toml
+++
date = '2025-06-30T19:15:39+02:00'
draft = false
title = 'Post title'
authors = ["Matt Britt"]
tags = ["homelab", "k3s", "GitOps"]
+++
```

Other fields already present in the repo include:

- `description` - short post/page description.
- `slug` - explicit slug for some older/bundled posts.
- `categories` - taxonomy list, often empty on older posts.
- `series` - used for multi-post series such as `LFCS` and `Kubernetes The Hard Way`.
- `featured` - theme-supported flag; currently used on the About page.
- `type = 'blank'` - used for non-standard pages such as About and Search.
- `indexable` - controls search indexing for theme/search pages.
- `externalLink` - present on some older posts, usually empty.

When editing existing posts, preserve the existing front matter style where practical. Some posts use quoted dates and some use bare TOML datetimes; avoid normalizing unrelated fields unless asked.

### Summaries

Most posts include a Hugo summary separator near the start:

```markdown
<!--more-->
```

Add it after the introductory paragraph for new posts so listing pages show a clean excerpt.

### Images in posts

For bundled post images, follow the existing root-relative URL style:

```markdown
![description](/posts/post-slug/image.png)
```

Examples:

```markdown
![pods](/posts/homelab-gitops-with-flux/pods.png)
![planned layout](/posts/arch-linux-disk-partitioning-and-encryption/plan.png)
```

Prefer meaningful alt text. Keep image files next to the post's `index.md` when they are post-specific.

### Existing content themes and series

Important recurring content groupings:

- Homelab/K3s/GitOps posts: cluster setup, Flux, Linkding, storage, Cloudflare tunnels, SOPS/age, Prometheus/Grafana, Traefik, Renovate.
- Talos Linux homelab posts: cluster setup, Flux boot/troubleshooting, Synology storage.
- LFCS preparation series: Linux commands, processes, logs, scheduling, users/groups, networking, storage, VMs/containers.
- Kubernetes The Hard Way series: Ansible prep, resources/certs/config, encryption/etcd/controllers, workers/kubectl/routes.
- Personal tooling and career posts: NixOS to Bluefin, dotfiles, Arch disk encryption, Dvorak, Vim, personal brand.
- Software architecture: CQRS.

Use existing taxonomy casing when extending a series or tag set. Examples currently include `homelab`, `k3s`, `k8s`, `K8S`, `GitOps`, `Talos`, `LFCS`, `Linux`, `Certificates`, `NixOS`, `Bluefin`, `arch`.

## Agent guidance

- Prefer content edits under `content/` for writing/post requests.
- Do not manually edit `public/` or `resources/`; they are generated.
- Keep site-wide behavior/configuration in `hugo.toml`.
- Avoid changing `themes/default/` unless the user explicitly asks to modify the vendored theme.
- For new blog posts, prefer a page bundle if images are involved; otherwise a single Markdown file is fine.
- Keep Markdown readable and consistent with the author's direct, practical voice.
- Preserve British/South African phrasing and the existing informal tone unless the user asks for a different style.
- After structural/config changes, run `hugo` when practical to verify the site builds.
