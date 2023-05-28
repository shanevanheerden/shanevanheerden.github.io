---
layout: page
permalink: /publications/
title: 📚 publications
description: Here's a list of my publications.
years: [2020, 2019, 2018]
nav: true
nav_order: 2
---
<!-- _pages/publications.md -->
<div class="publications">
{%- for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f {{ site.scholar.bibliography }} -q @*[year={{y}}]* %}
{% endfor %}
</div>
