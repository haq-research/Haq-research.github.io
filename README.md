# HAQ RESEARCH

Personal AI research blog with dark theme default and Doto (dot-matrix) font styling.

## Quick Deploy to GitHub Pages

```bash
# 1. Create repo named: your-username.github.io

# 2. Push this code
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_USERNAME/YOUR_USERNAME.github.io.git
git push -u origin main

# 3. Enable GitHub Pages: Settings → Pages → Source: main branch
```

Site will be live at `https://YOUR_USERNAME.github.io`

## Structure

```
├── _config.yml        # Site settings
├── _layouts/          # Templates
├── _posts/            # Blog posts (markdown)
├── _research/         # Research posts (markdown)
├── assets/css/        # Styling (Doto font + dark theme)
├── index.md           # Homepage
├── blogs.md           # Blogs listing
├── research.md        # Research listing
├── tags.md            # Tags page
└── about.md           # About page
```

## Adding Content

### New Blog Post
Create `_posts/YYYY-MM-DD-title.md`:
```yaml
---
layout: post
title: "Your Title"
date: 2026-03-15
tags: [AI, Architecture]
---
Your content...
```

### New Research Post
Create `_research/YYYY-MM-DD-title.md`:
```yaml
---
layout: research
title: "Paper Review Title"
date: 2026-03-15
tags: [Transformers]
research_type: "Paper Review"
paper_title: "Original Paper Title"
paper_authors: "Author et al., 2024"
paper_url: "https://arxiv.org/abs/..."
---
Your analysis...
```

## Features

- **Dark theme default** with light mode toggle
- **Doto font** (dot-matrix style) for headings
- **Responsive design**
- **Blogs + Research** sections
- **Tags** page

## License

Content © Abdul Haq
