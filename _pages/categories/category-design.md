---
title: "Design"
layout: archive
permalink: /categories/design/
author_profile: true
sidebar:
  nav: "docs"
---


{% assign posts = site.categories.Design %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}