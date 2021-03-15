---
title: Blog
permalink: /blog
layout: default
---

<link rel = "stylesheet" property = "stylesheet" href = "/assets/css/blog.css">

<div class = "posts">
{% assign postsByYear = site.posts | group_by_exp: "post", "post.date | date: '%Y'" %}

{% for year in postsByYear %}
<h1>{{ year.name }}</h1>
{% assign postsByMonth = year.items | group_by_exp: "post", "post.date | date: '%B'" %}
{% for month in postsByMonth %}
<h2>{{ month.name }}</h2>
<ul>
{% for post in month.items %}
<h3>{{ post.date | date: "%-d %B"}}</h3>
<div class = "post">
<a href = "{{ post.url }}">
<h3>{{ post.title }}</h3>
<p>{{ post.excerpt | markdownify }}</p>
</a>
</div>
{% endfor %}
</ul>
{% endfor %}
{% endfor %}
</div>