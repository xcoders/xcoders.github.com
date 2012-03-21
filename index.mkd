---
layout: default
title: index
---

{% for post in site.posts limit:3 %}
{% include postdetail.html %}
{% endfor %}


# Recent Posts

<ul class="posts">
	{% for post in site.posts offset:3 limit:5  %}
	<li><span>{{ post.date | date: "%b %d" }}</span> &raquo; <a href="{{ post.url }}">{{ post.title | xml_escape }}</a></li>
	{% endfor %}
</ul>

## [Archive](/archive.html)
