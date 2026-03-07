---
layout: default
title: Blogs
permalink: /blogs/
description: Long-form articles and short posts on AI systems and architecture.
---

<header class="page-header">
  <h1 class="page-title">Blogs</h1>
  <p class="page-description">Long-form articles and short posts on AI systems, LLM architecture, and engineering insights.</p>
</header>

{% assign postsByYear = site.posts | group_by_exp: "post", "post.date | date: '%Y'" %}

{% for year in postsByYear %}
<section class="year-group">
  <h2 class="year-title">{{ year.name }}</h2>
  <div class="content-list">
    {% for post in year.items %}
    <div class="archive-entry">
      <h3 class="archive-title">
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </h3>
      <span class="archive-date">{{ post.date | date: "%b %-d" }}</span>
    </div>
    {% endfor %}
  </div>
</section>
{% endfor %}
