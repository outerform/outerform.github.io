---
layout: page
permalink: /publications/
title: publications
description:
nav: true
nav_order: 2
---

<!-- _pages/publications.md -->

<!-- Bibsearch Feature -->

{% include bib_search.liquid %}

## Publications

<div class="publications">

{% bibliography --query @inproceedings,@article[abbr!=Preprint] %}

</div>

## Preprints

<div class="publications">

{% bibliography --query @article[abbr=Preprint] %}

</div>
