---
layout: default
permalink: /projects
---

# Projects
---

<ul class = "projects">
  {% for project in site.projects %}
    <a class = "project" href = "{{ project.link }}">
      <h2>{{ project.title }}</h2>
      <p>{{ project.content | markdownify }}</p>
    </a>
  {% endfor %}
</ul>

---
🛠: Work in Progress  
✅: Finished