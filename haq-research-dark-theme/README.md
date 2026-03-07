# HAQ RESEARCH

Personal AI research site using [al-folio](https://github.com/alshedivat/al-folio) theme.

## Quick Deploy

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_USERNAME/YOUR_USERNAME.github.io.git
git push -u origin main
```

Enable GitHub Pages: **Settings → Pages → Source: GitHub Actions**

## What You Manage

```
_config.yml              # Site settings (name, social links, theme)
_pages/about.md          # Your about page (pure markdown)
_posts/                  # Blog posts (pure markdown)
_bibliography/papers.bib # Research papers (BibTeX format)
_news/announcements.md   # News items
assets/img/prof_pic.jpg  # Your profile photo
```

## Adding Content

### New Blog Post

Create `_posts/YYYY-MM-DD-title.md`:

```markdown
---
layout: post
title: Your Title
date: 2026-03-15
description: Short description
tags: [ai, architecture]
categories: blog
---

Your pure markdown content here...
```

### New Research Paper

Add to `_bibliography/papers.bib`:

```bibtex
@article{author2024title,
  title={Paper Title},
  author={Author, Name},
  journal={Journal Name},
  year={2024},
  abstract={Your review/analysis of this paper.},
  html={https://arxiv.org/abs/...},
  selected={true}
}
```

### News Item

Add to `_news/announcements.md`:

```yaml
- date: 2026-03-15
  inline: "New blog post: **Title**"
```

## Theme Settings

Edit `_config.yml`:

- `color_scheme: dark` — Options: `dark`, `light`, `auto`
- `navbar_fixed: true` — Sticky header
- `github_username` / `linkedin_username` — Social links

## Profile Photo

Add your photo as `assets/img/prof_pic.jpg`

---

Theme: [al-folio](https://github.com/alshedivat/al-folio)
