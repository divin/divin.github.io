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


{% comment %}

TODO: For future me, if there are more posts!
<script>
    function show_hide(category)
    {
        var item = document.getElementById(category);
        if (item.style.display === "none")
        {
            item.style.display = "block";
        }
        else 
        {
            item.style.display = "none";
        }
    }
</script>

{% assign categories = "All|Announcement|Development|Philosophy|Music|Art" | split: "|" %}

<ul>
{% for category in categories %}
<button onclick = "show_hide('{{ category }}')">{{ category }}</button>
{% endfor %}
</ul>

{% for category in categories %}
<div id = "{{ category }}">

{% if category == "All" %}

{% else %}

<h1>{{ category }}</h1>

{% endif %}

</div>
{% endfor %}

{% endcomment %}