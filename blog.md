---
title: Blog
permalink: /blog
layout: default
---

## I'm sorry, this page is still under construction...

{% comment %}
<link rel = "stylesheet" property = "stylesheet" href = "/assets/css/blog.css">

{% assign postsByYear = site.posts | group_by_exp: "post", "post.date | date: '%Y'" %}

{% for year in postsByYear %}
<h1>{{ year.name }}</h1>
{% assign postsByMonth = year.items | group_by_exp: "post", "post.date | date: '%B'" %}
{% for month in postsByMonth %}
<h2>{{ month.name }}</h2>
<ul>
{% for post in month.items %}
<a href = "{{ post.url }}">
<p>{{ post.date | date: "%-d %B"}}</p>
<h2>{{ post.title }}</h2>
<p>{{ post.excerpt | markdownify }}</p>
</a>
{% endfor %}
</ul>
{% endfor %}
{% endfor %}
{% endcomment %}