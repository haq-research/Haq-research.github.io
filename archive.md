---
layout: page
title: Archive
permalink: /archive/
---

{% assign postsByYear = site.posts | group_by_exp: "post", "post.date | date: '%Y'" %}

{% for year in postsByYear %}
<h2 class="archive-year">{{ year.name }}</h2>
<ul class="archive-list">
  {% for post in year.items %}
  <li class="archive-item">
    <span class="archive-date">{{ post.date | date: "%b %d" }}</span>
    <span class="archive-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></span>
  </li>
  {% endfor %}
</ul>
{% endfor %}
