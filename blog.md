---
layout: default
permalink: /blog
---

# Blog
---

Recent posts:

<ul class = "blog">
  {% for post in site.posts %}
    <li class = "blog-post">
      <a class = "blog-link" href="{{ post.url }}">{{ post.date | date: "%-d %B %Y"}}: {{ post.title }}</a>
    </li>
  {% endfor %}
</ul>