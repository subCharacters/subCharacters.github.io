---
title: "DesignPatterns"
layout: archive
permalink: /categories/design-patterns/
author_profile: true
sidebar:
  nav: "docs"
---


{% assign posts = site.categories.DesignPatterns %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}