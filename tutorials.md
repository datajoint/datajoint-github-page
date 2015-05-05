---
layout: page
title: Tutorials
group: navigation
weight: 3
---

<table class='tutorialtable'>
	<thead><th>Title</th><th>Content</th></thead>
  {% assign post_list = site.posts | sort:"title" %}
  {% for post in post_list %}

	  {% if post.tags contains 'tutorial' %}
	  	<tr>
	    	<td><a href="{{ post.url }}">{{ post.title }}</a></td>
	    	<td>{{ post.summary }}</a></td>
	  	</tr>
	  {% endif %}

  {% endfor %}
</table>


