---
layout: page
title: Projects
permalink: /projects/index.html
---

{% for item in site.projects %}
  <span class="post-meta">Status: {{ item.status }}</span>
  <h3><a href="{{ item.url }}">{{ item.title }}</a></h3>
{% endfor %}
