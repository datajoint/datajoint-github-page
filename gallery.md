---
layout: page
title: Schema Gallery
group: navigation
weight: 5
---


<ul>
{% for post in posts %}
  {% if post.tags contains 'schema' %}
  <li>
    <a href="{{ post.url }}">{{ post.title }}</a>
  </li>
  {% endif %}
{% endfor %}
</ul>