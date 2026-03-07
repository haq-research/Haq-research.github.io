---
layout: default
title: Blogs
permalink: /blogs/
description: Long-form articles and short posts on AI systems, LLM architecture, and engineering insights.
---

<header class="page-header">
  <h1 class="page-title">Blogs</h1>
  <p class="page-description">
    Long-form articles and short posts on AI systems, LLM architecture, and engineering insights.
  </p>
</header>

{% assign postsByYear = site.posts | group_by_exp: "post", "post.date | date: '%Y'" %}

{% for year in postsByYear %}
<section class="year-group">
  <h2 class="year-title">{{ year.name }}</h2>
  <ul class="content-list">
    {% for post in year.items %}
    <li class="content-entry">
      <div class="entry-header">
        <h3 class="entry-title">
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </h3>
        <span class="entry-date">{{ post.date | date: "%b %-d" }}</span>
      </div>
      <p class="entry-excerpt">
        {{ post.excerpt | strip_html | truncatewords: 30 }}
      </p>
      <div class="entry-footer">
        {% if post.tags.size > 0 %}
        <div class="entry-tags">
          {% for tag in post.tags %}
          <span class="tag">{{ tag }}</span>
          {% endfor %}
        </div>
        {% endif %}
        <span class="read-time">{{ post.content | number_of_words | divided_by: 200 | plus: 1 }} min read</span>
      </div>
    </li>
    {% endfor %}
  </ul>
</section>
{% endfor %}
