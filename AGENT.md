# AGENT.md

## Repository summary

This repository is a Hugo static site for Matt Britt's personal blog, "Software Engineering & DevOps - Matt Britt". The site is configured in `hugo.toml` and uses the vendored Hugo theme in `themes/default` (`Rusty Typewriter`). Content focuses on homelab, Kubernetes, GitOps, Linux, DevOps, and engineering topics.

## Important paths

- `hugo.toml` - main Hugo site configuration, menus, taxonomies, social links, output formats, and theme parameters.
- `content/posts/` - blog posts. Posts are either single Markdown files or page bundles with an `index.md` and colocated images.
- `content/about/index.md` - about page content.
- `content/search.md` - search page content.
- `archetypes/default.md` - default front matter used by `hugo new`.
- `data/rtwt/content.yaml` - theme content snippets, including homepage text.
- `static/` - static assets copied directly into the site.
- `themes/default/` - vendored Rusty Typewriter theme. Avoid editing unless the theme itself needs changing.
- `public/` and `resources/` - generated Hugo output/cache. Do not edit by hand.

## Development commands

Run these from the repository root:

```bash
hugo server
```

Starts the local development server with live reload.

```bash
hugo
```

Builds the static site into `public/`.

If drafts should be included locally:

```bash
hugo server -D
```

## Content conventions

Most posts use TOML front matter delimited by `+++`:

```toml
+++
date = '2025-06-30T19:15:39+02:00'
draft = false
title = 'Post title'
authors = ["Matt Britt"]
tags = ["homelab", "k3s", "GitOps"]
+++
```

Optional fields seen in the repo include `description` and theme-supported fields such as `featured`.

Use `<!--more-->` near the beginning of posts to define the summary break for listing pages.

For posts with images, prefer Hugo page bundles:

```text
content/posts/my-post-slug/
  index.md
  image.png
```

Reference bundled images with root-relative paths matching existing style, for example:

```markdown
![pods](/posts/homelab-gitops-with-flux/pods.png)
```

## Agent guidance

- Do not manually modify generated files under `public/` or Hugo cache files under `resources/`.
- Keep site-wide configuration changes in `hugo.toml`.
- Prefer adding or editing Markdown under `content/` for blog changes.
- Preserve existing front matter style and taxonomy names (`tags`, `categories`, `series`, `authors`).
- Be cautious when changing `themes/default/`; site-level overrides are preferable when possible.
