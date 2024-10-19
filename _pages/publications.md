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


<h2>Work in Progress</h2>
<hr>

{% bibliography --query @workinginprogress%}

</div>


