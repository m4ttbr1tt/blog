# AGENT.md

## Repository overview

This repository is a Hugo static site for Matt Britt's personal blog, **"Software Engineering & DevOps - Matt Britt"**. It is configured by `hugo.toml` and uses the vendored Hugo theme at `themes/default` (`Rusty Typewriter`).

The blog focuses on homelab infrastructure, Kubernetes/K3s/Talos, GitOps, Linux, DevOps/SRE practices, software engineering, and personal tooling.

## Key paths

- `hugo.toml` - main Hugo configuration: site title/header, theme, pagination, syntax highlighting, taxonomies, menus, social links, search output, and Rusty Typewriter theme params.
- `content/` - primary authoring area for pages and posts.
- `content/about/index.md` - About/start-here page. Uses `type = 'blank'`, `indexable = true`, and `featured = true`.
- `content/search.md` - Search page. Uses `type = 'blank'`, `indexable = false`, and the `{{< search >}}` shortcode.
- `content/posts/` - blog posts.
- `archetypes/default.md` - default front matter used by `hugo new`.
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

## Content conventions

Most requested writing changes should happen under `content/`.

Posts use either:

```text
content/posts/post-slug.md
```

or a page bundle when local images/assets are needed:

```text
content/posts/post-slug/
  index.md
  image.png
```

Use TOML front matter delimited with `+++`. Common fields are:

```toml
+++
date = '2025-06-30T19:15:39+02:00'
draft = false
title = 'Post title'
authors = ["Matt Britt"]
tags = ["homelab", "k3s", "GitOps"]
+++
```

Other fields already present include `description`, `slug`, `categories`, `series`, `featured`, `type`, `indexable`, and `externalLink`.

When editing existing posts, preserve the existing front matter style where practical. Some posts use quoted dates and some use bare TOML datetimes; avoid normalising unrelated fields unless asked.

Most posts include a Hugo summary separator after the opening paragraph:

```markdown
<!--more-->
```

For bundled post images, use root-relative paths:

```markdown
![description](/posts/post-slug/image.png)
```

Prefer meaningful alt text and keep post-specific images beside the post's `index.md`.

## Writing style

Write from Matt's perspective. The site is a personal technical blog and public engineering log, not vendor documentation.

The tone should be:

- first-person and personal;
- direct and practical;
- informal but technical;
- concise, with little unnecessary background;
- honest about problems, trade-offs, and changes of mind;
- grounded in what was built, tried, learned, or changed.

Useful patterns:

- Start with the immediate context: what was done, what problem came next, or why the topic matters.
- Keep the intro short and add `<!--more-->` early.
- Move quickly into commands, configuration, screenshots, or concrete steps.
- Use simple Markdown headings.
- Use fenced code blocks for shell commands, YAML, and configuration.
- Include links to official docs, tools, GitHub repos, or references when useful.
- Prefer plain language over academic or marketing language.

For new posts, aim for **250-500 words** unless the topic needs a longer command-heavy walkthrough. Very short updates around **120-200 words** are also fine when appropriate.

## Common content areas

Recurring content groupings include:

- Homelab/K3s/GitOps: cluster setup, Flux, Linkding, storage, Cloudflare tunnels, SOPS/age, Prometheus/Grafana, Traefik, Renovate.
- Talos Linux homelab: cluster setup, Flux boot/troubleshooting, Synology storage.
- LFCS/Linux study notes: commands, processes, logs, scheduling, users/groups, networking, storage, VMs/containers.
- Kubernetes The Hard Way: Ansible prep, resources/certs/config, encryption/etcd/controllers, workers/kubectl/routes.
- Personal tooling and workflow: NixOS, Bluefin, Arch Linux, disk encryption, dotfiles, DevPods, Vim, Dvorak.
- Career and software engineering reflections: personal brand building, CQRS, engineering practices, and public learning.

Use existing taxonomy casing when extending a series or tag set. Examples currently include `homelab`, `k3s`, `k8s`, `K8S`, `GitOps`, `Talos`, `LFCS`, `Linux`, `Certificates`, `NixOS`, `Bluefin`, and `arch`.

## Agent guidance

- Prefer content edits under `content/` for writing/post requests.
- Do not manually edit `public/` or `resources/`; they are generated.
- Keep site-wide behaviour/configuration in `hugo.toml`.
- Avoid changing `themes/default/` unless explicitly asked to modify the vendored theme.
- For new blog posts, prefer a page bundle if images are involved; otherwise a single Markdown file is fine.
- Preserve British/South African phrasing and the existing informal tone unless asked for a different style.
- After structural/config changes, run `hugo` when practical to verify the site builds.

## Things to avoid

- Do not make posts sound like vendor documentation.
- Do not add excessive background history unless it is personally relevant.
- Do not over-polish the voice into corporate blog style.
- Do not remove the practical, note-taking quality of the existing posts.
- Do not edit generated content in `public/` or `resources/`.
