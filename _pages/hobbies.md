---
layout: page
title: 🎨 hobbies
permalink: /hobbies/
description: Here are some of my hobbies outside of work.
nav: true
nav_order: 4
horizontal: false
---

### Under construction.

<div class="projects">
{%- if site.enable_project_categories and page.display_categories %}
  <!-- Display categorized projects -->
  {%- for category in page.display_categories %}
  <h2 class="category">{{ category }}</h2>
  {%- assign categorized_projects = site.projects | where: "category", category -%}
  {%- assign sorted_projects = categorized_projects | sort: "importance" %}
  <!-- Generate cards for each project -->
  {% if page.horizontal -%}
  <div class="container">
    <div class="row row-cols-2">
    {%- for project in sorted_projects -%}
      {%- if project.page_type == "hobby" -%}
        {% include projects_horizontal.html %}
      {%- endif -%}
    {%- endfor %}
    </div>
  </div>
  {%- else -%}
  <div class="grid">
    {%- for project in sorted_projects -%}
      {%- if project.page_type == "hobby" -%}
        {% include projects.html %}
      {%- endif -%}
    {%- endfor %}
  </div>
  {%- endif -%}
  {% endfor %}

{%- else -%}
<!-- Display projects without categories -->
  {%- assign sorted_projects = site.projects | sort: "importance" -%}
  <!-- Generate cards for each project -->
  {% if page.horizontal -%}
  <div class="container">
    <div class="row row-cols-2">
    {%- for project in sorted_projects -%}
      {%- if project.page_type == "hobby" -%}
        {% include projects_horizontal.html %}
      {%- endif -%}
    {%- endfor %}
    </div>
  </div>
  {%- else -%}
  <div class="grid">
    {%- for project in sorted_projects -%}
      {%- if project.page_type == "hobby" -%}
        {% include projects.html %}
      {%- endif -%}
    {%- endfor %}
  </div>
  {%- endif -%}
{%- endif -%}
</div>
