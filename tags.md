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
    {% if post.tags contains tag %}{% assign tag_count = tag_count | plus: 1 %}{% endif %}
  {% endfor %}
  {% for paper in site.research %}
    {% if paper.tags contains tag %}{% assign tag_count = tag_count | plus: 1 %}{% endif %}
  {% endfor %}
  <a href="#{{ tag | slugify }}" class="tag-item">{{ tag }}<span class="tag-count">({{ tag_count }})</span></a>
  {% endif %}
{% endfor %}
</div>

{% for tag in all_tags %}
{% if tag %}
<section class="year-group">
  <h2 id="{{ tag | slugify }}" class="year-title">{{ tag }}</h2>
  <div class="content-list">
    {% for post in site.posts %}
    {% if post.tags contains tag %}
    <div class="archive-entry">
      <h3 class="archive-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
      <span class="archive-date">{{ post.date | date: "%b %Y" }}</span>
    </div>
    {% endif %}
    {% endfor %}
    {% for paper in site.research %}
    {% if paper.tags contains tag %}
    <div class="archive-entry">
      <h3 class="archive-title"><a href="{{ paper.url | relative_url }}">{{ paper.title }}</a></h3>
      <span class="archive-date">{{ paper.date | date: "%b %Y" }}</span>
    </div>
    {% endif %}
    {% endfor %}
  </div>
</section>
{% endif %}
{% endfor %}
