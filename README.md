# Haq Research

Personal AI research blog covering AI systems architecture, LLM platforms, and scalable AI infrastructure.

**Live site:** [https://haq-research.github.io](https://haq-research.github.io)

---

## 🚀 Quick Start: Deploy to GitHub Pages

### Step 1: Create GitHub Repository

1. Go to [github.com/new](https://github.com/new)
2. Name it: `haq-research.github.io`
3. Make it **Public**
4. Don't initialize with README

### Step 2: Push This Code

```bash
cd haq-research-site

git init
git add .
git commit -m "Initial commit"

git remote add origin https://github.com/YOUR_USERNAME/haq-research.github.io.git
git branch -M main
git push -u origin main
```

### Step 3: Enable GitHub Pages

1. Repository → **Settings** → **Pages**
2. Source: **Deploy from a branch**
3. Branch: `main` / `/ (root)`
4. Click **Save**

Your site will be live at `https://YOUR_USERNAME.github.io` in ~2 minutes!

---

## 📁 Project Structure

```
haq-research-site/
├── _config.yml              # Site configuration
├── _layouts/
│   ├── default.html         # Base layout
│   ├── home.html            # Homepage
│   ├── post.html            # Blog post layout
│   ├── research.html        # Research post layout
│   └── page.html            # Static pages
├── _posts/                  # Blog posts
│   └── 2026-03-07-example.md
├── _research/               # Research posts
│   └── 2026-02-28-example.md
├── assets/css/
│   └── style.css            # All styles
├── index.md                 # Homepage
├── blogs.md                 # Blogs listing
├── research.md              # Research listing
├── tags.md                  # Tags page
├── about.md                 # About page
└── README.md
```

---

## ✍️ Adding Content

### New Blog Post

Create `_posts/YYYY-MM-DD-title.md`:

```markdown
---
layout: post
title: "Your Blog Title"
date: 2026-03-15
tags: [AI, Architecture]
---

Your content here...
```

### New Research Post

Create `_research/YYYY-MM-DD-title.md`:

```markdown
---
layout: research
title: "Paper Review Title"
date: 2026-03-15
tags: [Transformers, Architecture]
research_type: "Paper Review"
paper_title: "Original Paper Title"
paper_authors: "Author et al., 2024"
paper_url: "https://arxiv.org/abs/..."
---

Your analysis here...
```

**Research types:** Paper Review, Survey Analysis, Technical Analysis

---

## 🎨 Customization

### Site Info

Edit `_config.yml`:

```yaml
title: Haq Research
author:
  name: Abdul Haq
  linkedin: https://linkedin.com/in/...
  github: https://github.com/...
```

### Styling

Edit `assets/css/style.css`. Key variables:

```css
:root {
  --accent: #1a73e8;
  --content-width: 750px;
}
```

### Dark Mode

Built-in! Toggle with 🌙 button. Colors in `[data-theme="dark"]`.

---

## 🔧 Local Development

```bash
# Install dependencies
bundle install

# Run local server
bundle exec jekyll serve

# Open http://localhost:4000
```

---

## 📝 Content Types

| Type | Location | Label Color |
|------|----------|-------------|
| Blog | `_posts/` | Blue |
| Research | `_research/` | Amber |

---

## License

Content © Abdul Haq. Code is MIT licensed.
