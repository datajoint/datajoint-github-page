---
layout: page
title: Blog
group: navigation
weight: 2
---




{% for post in site.posts limit 4 %}
  {% if post.tags contains 'blog' %}
    
<h3>{{ post.date | date_to_string }} &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h3>
    
{{ post.content | strip_html | truncatewords:50}}<br>	<a href="{{ post.url }}">Read more...</a><br><br>
  {% endif %}
{% endfor %}

