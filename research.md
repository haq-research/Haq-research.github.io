---
layout: default
title: Research
permalink: /research/
description: Reviews and technical insights on influential AI research papers.
---

<header class="page-header">
  <h1 class="page-title">Research</h1>
  <p class="page-description">
    Reviews and technical insights on influential AI research papers, with practical takeaways for production systems.
  </p>
</header>

<ul class="research-list">
  {% for paper in site.research %}
  <li class="research-entry">
    <span class="entry-type research">{{ paper.research_type | default: "Paper Review" }}</span>
    <h2 class="entry-title">
      <a href="{{ paper.url | relative_url }}">{{ paper.title }}</a>
    </h2>
    {% if paper.paper_title %}
    <div class="paper-reference">
      <strong>Paper:</strong> {{ paper.paper_authors }} — "{{ paper.paper_title }}"
      {% if paper.paper_url %}<a href="{{ paper.paper_url }}" class="paper-link" target="_blank">arXiv →</a>{% endif %}
    </div>
    {% endif %}
    <div class="entry-meta">
      <span>{{ paper.date | date: "%B %-d, %Y" }}</span>
      <span class="separator">|</span>
      <span>{{ paper.content | number_of_words | divided_by: 200 | plus: 1 }} min read</span>
    </div>
    <p class="entry-excerpt">
      {{ paper.excerpt | strip_html | truncatewords: 40 }}
    </p>
    {% if paper.tags.size > 0 %}
    <div class="entry-tags">
      {% for tag in paper.tags %}
      <span class="tag">{{ tag }}</span>
      {% endfor %}
    </div>
    {% endif %}
  </li>
  {% endfor %}
</ul>
