---
layout: default
title: home
---

{% for post in site.posts limit:5 %}
{% include postdetail.html %}
{% endfor %}
  
  
# Recent Posts

{% assign posts = site.posts limit:10 %}
{% include postlist.html %}

##### [Archive](archive.html)
