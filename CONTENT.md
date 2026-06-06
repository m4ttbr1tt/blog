# CONTENT.md

## Content review

This site is a personal technical blog. The content in `content/` is written from Matt Britt's perspective as a software engineer/DevOps practitioner documenting what he is learning, building, and changing in his own setup.

The main subject areas are:

- **Homelab infrastructure** - K3s clusters, Talos Linux, GitOps, Flux, storage, networking, monitoring, secrets, ingress, and automation.
- **Linux and certification study** - LFCS preparation notes covering commands, files, processes, logs, users/groups, networking, storage, containers, and scheduling.
- **Kubernetes learning** - Kubernetes The Hard Way notes, including Ansible preparation, certificates/config, etcd/controllers, workers, kubectl, and routes.
- **Personal tooling and workflow** - NixOS, Bluefin, Arch Linux, disk encryption, dotfiles, DevPods, Vim, Dvorak, and keyboard-driven work.
- **Career and software engineering reflections** - personal brand building, CQRS, engineering practices, and public learning.

## Structure of the content directory

- `content/about/index.md` - short personal introduction and purpose statement for the blog.
- `content/search.md` - search page using the theme shortcode.
- `content/posts/` - all blog posts.

Posts are a mix of:

- single Markdown files, for posts without local assets;
- Hugo page bundles, where `index.md` sits beside screenshots or diagrams.

The posts are mostly practical notes rather than long-form essays. Many read like a public engineering log: what was attempted, why it was needed, what commands/configuration were used, and what the next step is.

## Existing post length

Posts are generally short to medium length.

Approximate observed ranges:

- Very short posts: around **120-200 words**.
- Typical posts: around **250-500 words**.
- Longer technical notes: around **700-950 words**.

The longer posts are usually command-heavy tutorials or certification notes. The shorter posts tend to be status updates, opinions, or concise setup notes.

A good target for new posts is:

- **250-500 words** for a normal update or practical walkthrough.
- **700-900 words** only when the topic needs several commands, examples, tables, or troubleshooting notes.

## Writing style

The tone is:

- first-person and personal;
- direct and practical;
- informal but technical;
- concise, with little unnecessary background;
- honest about problems, changes of mind, and trade-offs;
- written as a learning/building journal rather than polished corporate documentation.

Common stylistic patterns:

- Start with the immediate context: what was done, what problem came next, or why the topic matters.
- Use phrases like “Today I…”, “Next step…”, “In the last post…”, “I ran into…”, “I want to…”.
- Explain just enough theory before moving to commands or config.
- Use Markdown headings to break up the steps.
- Use code blocks heavily for shell commands, YAML, and configuration.
- Include links to official docs, tools, GitHub repos, or references.
- Use occasional enthusiasm and personality: “beautiful”, “kinda sucks”, “good to brush up”, emoji in some posts.
- Prefer plain language over academic or marketing language.

## Voice guidelines for new content

When writing in this repo's tone:

1. **Write in first person**

   Use Matt's point of view. The blog is a personal record of learning and building.

   Good:

   ```markdown
   I wanted to get storage provisioning working before deploying anything stateful.
   ```

   Avoid:

   ```markdown
   This article provides a comprehensive enterprise-grade overview of storage provisioning.
   ```

2. **Keep the intro short**

   Start with what changed, what is being built, or what problem needs solving. Avoid long generic introductions.

3. **Make it practical quickly**

   After a short explanation, move into commands, configuration, screenshots, or concrete steps.

4. **Use simple headings**

   Existing posts use clear headings such as:

   - `### Talos Linux`
   - `### Encryption`
   - `#### User management`
   - `### Ways to structure your git repo`

5. **Include the why, but do not over-explain**

   A short reason is enough. The content should feel useful and grounded in actual work.

6. **Be honest about trade-offs and friction**

   It is fine to say that something was annoying, confusing, brittle, or not worth the time. Several posts are reflective and candid rather than neutral.

7. **Use code blocks for repeatable steps**

   Shell examples should normally use fenced code blocks:

   ```markdown
   ```shell
   kubectl get pods -A
   ```
   ```

   YAML/config snippets should also be fenced with the correct language where possible.

8. **Use `<!--more-->` after the opening section**

   Most posts include a summary break after the opening paragraph or two.

9. **Prefer concise conclusions**

   Posts do not need a formal conclusion. A short “next step” or closing observation is enough.

## Content-specific guidance

### Homelab posts

Homelab posts should read like an implementation journal. They often reference the previous step and then show the next piece of the system.

Useful pattern:

```markdown
The app is now running, but the next problem is persistence/networking/secrets/monitoring.

<!--more-->

A manual setup works, but that goes against the GitOps approach, so I want this stored in git and reconciled by Flux.

### Config

```yaml
...
```
```

Use real commands and manifests where possible. Mention the goal first, then show the configuration.

### LFCS/Linux study posts

These posts are more like study notes. They are command-heavy, grouped by subtopic, and concise.

Use:

- short explanations;
- many shell command examples;
- comments inside code blocks;
- headings for exam objective areas.

Do not turn these into long tutorials unless asked.

### Kubernetes The Hard Way posts

These posts should connect practical steps to learning Kubernetes internals. They can include scripts, configuration, and links to the related GitHub notes.

Keep the tone focused on understanding how Kubernetes works under the hood.

### Personal workflow/opinion posts

These are more reflective. They should still be direct and specific, using personal examples rather than abstract advice.

Good themes:

- what changed;
- why the old approach stopped working;
- what was learned;
- what trade-off is being accepted now.

## Markdown and formatting conventions

- Use TOML front matter with `+++` delimiters.
- Use `authors = ["Matt Britt"]` on posts unless there is a reason not to.
- Use tags and series consistently with existing posts.
- Keep paragraphs short.
- Use bullet lists where they make the point clearer.
- Use tables when comparing hosts, roles, IPs, or configuration states.
- Use root-relative image paths for bundled images, for example:

```markdown
![dashboard](/posts/talos-linux-flux-boot-and-troubleshooting/dashboard.png)
```

## Things to avoid

- Do not make posts sound like vendor documentation.
- Do not add excessive background history unless it is personally relevant.
- Do not over-polish the voice into corporate blog style.
- Do not remove the practical, note-taking quality of the existing posts.
- Do not edit generated content in `public/` or `resources/`.
