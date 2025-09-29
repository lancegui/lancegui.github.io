---
layout: page
permalink: /research/
title: Research
nav: true
nav_order: 2
---

<!-- _pages/publications.md -->

<!-- Bibsearch Feature -->

<!-- {% include bib_search.liquid %} -->

<div class="publications">

<h2>Working Papers</h2>
<hr>

{% bibliography --query @workingpaper%}

<div style="text-align: right;">
  <sub style="font-size: 0.9em;">* Scheduled. † Presented by coauthor.</sub>
</div>

<h2>Work in Progress</h2>
<hr>

{% bibliography --query @workinginprogress%}

</div>


