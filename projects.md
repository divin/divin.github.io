---
title: Projects
permalink: /projects
layout: default
---

<link rel = "stylesheet" property = "stylesheet" href = "/assets/css/projects.css">

<ul class = "projects">
  {% for project in site.projects %}
    <a class = "project" href = "{{ project.link }}">
      <h2>{{ project.title }} {% if project.wip %} 🛠 {% else %} ✅ {% endif %}</h2>
      <p>{{ project.excerpt | markdownify }}</p>
      <details>
        <summary>More Info</summary>
        {{ project.content | markdownify }}
      </details>
    </a>
  {% endfor %}
</ul>

---
🛠: Work in Progress  
✅: Finished