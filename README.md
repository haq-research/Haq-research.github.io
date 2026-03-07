# Haq Research

Personal AI research blog covering AI systems architecture, LLM platforms, and scalable AI infrastructure.

**Live site:** [https://haq-research.github.io](https://haq-research.github.io)

---

## 🚀 Quick Start: Deploy to GitHub Pages

### Step 1: Create GitHub Repository

1. Go to [github.com/new](https://github.com/new)
2. Name it exactly: `haq-research.github.io` (or `<your-username>.github.io`)
3. Make it **Public**
4. Don't initialize with README (you'll push this code)

### Step 2: Push This Code

```bash
# Clone or download this folder, then:
cd haq-research-site

# Initialize git
git init
git add .
git commit -m "Initial commit: Haq Research site"

# Add your remote and push
git remote add origin https://github.com/haq-research/haq-research.github.io.git
git branch -M main
git push -u origin main
```

### Step 3: Enable GitHub Pages

1. Go to your repository → **Settings** → **Pages**
2. Source: **Deploy from a branch**
3. Branch: `main` / `/ (root)`
4. Click **Save**

Your site will be live at `https://haq-research.github.io` within a few minutes!

---

## 📁 Project Structure

```
haq-research-site/
├── _config.yml          # Site configuration
├── _layouts/            # Page templates
│   ├── default.html     # Base layout
│   ├── home.html        # Homepage with post list
│   ├── post.html        # Individual blog post
│   └── page.html        # Static pages
├── _posts/              # Blog posts (add new posts here!)
│   └── 2026-03-07-evolution-of-ai-interaction-systems.md
├── assets/
│   └── css/
│       └── style.css    # All styles
├── index.md             # Homepage
├── about.md             # About page
├── archive.md           # Post archive
├── tags.md              # Tags page
├── Gemfile              # Ruby dependencies
└── README.md            # This file
```

---

## ✍️ Writing New Posts

Create a new file in `_posts/` with this naming format:

```
YYYY-MM-DD-your-post-title.md
```

Example: `_posts/2026-03-15-rag-architecture-patterns.md`

### Post Template

```markdown
---
layout: post
title: "Your Post Title"
date: 2026-03-15
tags: [RAG, Architecture, LLM]
---

Your content here. Supports full Markdown:

## Headings

**Bold**, *italic*, `code`

- Bullet lists
- Like this

```python
# Code blocks with syntax highlighting
def hello():
    return "Hello, world!"
```

> Blockquotes for emphasis

[Links](https://example.com) work too.
```

---

## 🎨 Customization

### Site Info
Edit `_config.yml`:
```yaml
title: Haq Research
description: Your description
author:
  name: Abdul Haq
  linkedin: https://linkedin.com/in/...
  github: https://github.com/...
```

### Styling
All styles are in `assets/css/style.css`. Key variables at the top:
```css
:root {
  --accent: #1a73e8;      /* Link color */
  --content-width: 750px;  /* Max content width */
  /* ... */
}
```

### Dark Mode
Dark mode is built-in! Users can toggle with the 🌙 button. Colors are defined in `[data-theme="dark"]` section.

---

## 🔧 Local Development (Optional)

If you want to preview changes locally before pushing:

```bash
# Install Ruby and Bundler first, then:
bundle install
bundle exec jekyll serve

# Open http://localhost:4000
```

---

## 📝 Adding Whitepapers/Research

For research papers, create a `research/` folder and add markdown files:

```
research/
├── index.md              # Research listing page
├── rag-enterprise.md     # Individual paper
└── llm-evaluation.md
```

---

## 🔗 Connecting Custom Domain (Optional)

1. Add a `CNAME` file with your domain: `haqresearch.com`
2. Configure DNS:
   - A record: `185.199.108.153`
   - A record: `185.199.109.153`
   - CNAME: `www` → `haq-research.github.io`

---

## License

Content © Abdul Haq. Code is MIT licensed.
