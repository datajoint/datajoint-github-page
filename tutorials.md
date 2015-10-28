---
layout: page
title: Tutorials
group: navigation
weight: 3
---

<table class='tutorialtable'>
	<thead><th>Title</th><th>Content</th><th colspan="2">Languages</th></thead>
  {% assign post_list = site.posts | sort:"title" %}
  {% for post in post_list %}

	  {% if post.tags contains 'tutorial' %}
	  	<tr>
	    	<td><a href="{{ post.url }}">{{ post.title }}</a></td>
	    	<td>{{ post.summary }}</a></td>

			<td>
				{% if post.tags contains 'python' %} <img src="/images/python.jpeg"> {% endif %}
			</td>
			<td>
				{% if post.tags contains 'matlab' %} <img src="/images/matlab.jpeg"> {% endif %}
			</td>	
	  	</tr>
	  {% endif %}

  {% endfor %}
</table>


