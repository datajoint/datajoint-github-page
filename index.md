---
layout: page
title: DataJoint
tagline: using data relationally in the science lab
---
{% include JB/setup %}

DataJoint for [MATLAB](http://datajoint.github.io/datajoint-matlab/) and [Python](https://github.com/datajoint/datajoint-python) is a high-level programming interface for MySQL databases to support data processing chains in science labs. DataJoint is built on the foundation of the relational data model and prescribes a consistent method for organizing, populating, and querying data.

This blog contains relevant news, code examples, and discussions.

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
