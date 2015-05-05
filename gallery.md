---
layout: page
title: Schema Gallery
group: navigation
weight: 5
---

If you want to contribute your schema to the gallery, please 

* fork this page on [github](https://github.com/datajoint/datajoint.github.io).
* add another post with the tag 'schema' to `_posts` (just look at the examples).
* commit, push, and send us a pull request. 

{% assign post_list = site.posts | sort:"title" %}

### Matlab

<table class='schematable'>
	<thead><th>Title</th><th>Summary</th><th>Author</th></thead>
  {% assign post_list = site.posts | sort:"title" %}
  {% for post in post_list %}

	  {% if post.tags contains 'schema' and post.tags contains 'matlab' %}
	  	<tr>
	    	<td class='schematable'><a href="{{ post.url }}">{{ post.title }}</a></td>
	    	<td class='schematable'>{{ post.summary }}</a></td>
	    	<td class='schematable'>{{ post.author }}</a></td>
	  	</tr>
	  {% endif %}

  {% endfor %}
</table>

<!-- 

<ul>
  {% for post in post_list %}

	  {% if post.tags contains 'schema' and post.tags contains 'matlab' %}
	  <li>
	    <a href="{{ post.url }}">{{ post.title }}</a>
	  </li>
	  {% endif %}

  {% endfor %}
</ul> -->

### Python

<table class='schematable'>
	<thead><th>Title</th><th>Summary</th><th>Author</th></thead>
  {% assign post_list = site.posts | sort:"title" %}
  {% for post in post_list %}

	  {% if post.tags contains 'schema' and post.tags contains 'python' %}
	  	<tr>
	    	<td class='schematable'><a href="{{ post.url }}">{{ post.title }}</a></td>
	    	<td class='schematable'>{{ post.summary }}</a></td>
	    	<td class='schematable'>{{ post.author }}</a></td>
	  	</tr>
	  {% endif %}

  {% endfor %}
</table>
