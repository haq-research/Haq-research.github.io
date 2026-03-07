# HAQ RESEARCH

Hugo site with PaperMod theme + Doto font.

## Deploy to GitHub Pages

1. **Create repo** named `your-username.github.io`
2. **Push this code:**
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
   git add .
   git commit -m "Add PaperMod theme"
   git remote add origin https://github.com/YOUR_USERNAME/YOUR_USERNAME.github.io.git
   git push -u origin main
   ```
3. **Settings → Pages → Source: GitHub Actions**
4. Site builds automatically

## Structure

```
hugo.yaml              ← Site config
content/
  about.md             ← About page
  posts/               ← Blog posts (markdown)
  research/            ← Research posts (markdown)
static/css/custom.css  ← Doto font (don't edit)
layouts/partials/      ← Theme override for custom CSS
```

## Adding Content

### New Blog Post

Create `content/posts/your-title.md`:

```markdown
---
title: "Your Title"
date: 2026-03-15
tags: ["AI", "RAG"]
summary: "Short description"
---

Your markdown content...
```

### New Research Post

Create `content/research/paper-name.md`:

```markdown
---
title: "Paper Review Title"
date: 2026-03-15
tags: ["Transformers", "Paper Review"]
summary: "Short description"
---

**Paper:** Author et al., 2024 — [arXiv](https://arxiv.org/abs/...)

---

Your analysis...
```

## Local Development

```bash
hugo server -D
```

## Features

- ✅ Dark theme default
- ✅ Doto font on headings/nav
- ✅ PaperMod theme (same as Lil'Log)
- ✅ Pure markdown content
