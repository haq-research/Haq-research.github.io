# HAQ RESEARCH

Personal AI research site using [Minimal Mistakes](https://github.com/mmistakes/minimal-mistakes) theme.

## Deploy

1. **Delete everything in your repo** and push these files
2. Settings → Pages → Source: **Deploy from a branch** → **main** → Save
3. Wait 1-2 minutes for build

## What You Manage

```
_config.yml        ← Site settings
_pages/about.md    ← About page (markdown)
_posts/            ← Blog posts (markdown)
index.md           ← Homepage
```

## Adding a Blog Post

Create `_posts/YYYY-MM-DD-title.md`:

```markdown
---
layout: single
title: "Your Title"
date: 2026-03-15
categories: blog
tags: [ai, rag]
---

Your markdown content...
```

## Theme

- **Dark mode** enabled
- **Doto font** for headings (via `assets/css/main.scss`)
