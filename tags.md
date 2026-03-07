---
layout: default
title: Tags
permalink: /tags/
---

<header class="page-header">
  <h1 class="page-title">Tags</h1>
  <p class="page-description">Browse content by topic.</p>
</header>

{% assign all_tags = site.posts | map: "tags" | concat: site.research | map: "tags" | flatten | compact | uniq | sort %}

<div class="tags-cloud">
{% for tag in all_tags %}
  {% if tag %}
  {% assign tag_count = 0 %}
  {% for post in site.posts %}
    {% if post.tags contains tag %}
      {% assign tag_count = tag_count | plus: 1 %}
    {% endif %}
  {% endfor %}
  {% for paper in site.research %}
    {% if paper.tags contains tag %}
      {% assign tag_count = tag_count | plus: 1 %}
    {% endif %}
  {% endfor %}
  <a href="#{{ tag | slugify }}" class="tag-item">
    {{ tag }}<span class="tag-count">({{ tag_count }})</span>
  </a>
  {% endif %}
{% endfor %}
</div>

<hr style="margin: 48px 0; border: none; border-top: 1px solid var(--border);">

{% for tag in all_tags %}
{% if tag %}
<section class="tag-section">
  <h2 id="{{ tag | slugify }}" class="tag-section-title">{{ tag }}</h2>
  <ul class="content-list">
    {% for post in site.posts %}
    {% if post.tags contains tag %}
    <li class="content-entry">
      <div class="entry-header">
        <h3 class="entry-title">
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </h3>
        <span class="entry-date">{{ post.date | date: "%b %-d, %Y" }}</span>
      </div>
      <span class="entry-type blog" style="font-size: 0.65rem;">Blog</span>
    </li>
    {% endif %}
    {% endfor %}
    {% for paper in site.research %}
    {% if paper.tags contains tag %}
    <li class="content-entry">
      <div class="entry-header">
        <h3 class="entry-title">
          <a href="{{ paper.url | relative_url }}">{{ paper.title }}</a>
        </h3>
        <span class="entry-date">{{ paper.date | date: "%b %-d, %Y" }}</span>
      </div>
      <span class="entry-type research" style="font-size: 0.65rem;">Research</span>
    </li>
    {% endif %}
    {% endfor %}
  </ul>
</section>
{% endif %}
{% endfor %}
