---
title: "other"
layout: archive
permalink: /categories/other/
author_profile: true
sidebar:
  nav: "docs"
---


{% assign posts = site.categories.other %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}