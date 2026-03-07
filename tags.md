---
layout: page
title: Tags
permalink: /tags/
---

{% assign tags = site.posts | map: "tags" | uniq | sort %}

<div class="tags-cloud">
{% for tag in tags %}
  {% if tag %}
  <a href="#{{ tag | slugify }}" class="tag-item">
    {{ tag }}
    <span class="tag-count">({{ site.posts | where_exp: "post", "post.tags contains tag" | size }})</span>
  </a>
  {% endif %}
{% endfor %}
</div>

---

{% for tag in tags %}
{% if tag %}
<h2 id="{{ tag | slugify }}" class="archive-year">{{ tag }}</h2>
<ul class="archive-list">
  {% assign tagged_posts = site.posts | where_exp: "post", "post.tags contains tag" %}
  {% for post in tagged_posts %}
  <li class="archive-item">
    <span class="archive-date">{{ post.date | date: "%b %d, %Y" }}</span>
    <span class="archive-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></span>
  </li>
  {% endfor %}
</ul>
{% endif %}
{% endfor %}
