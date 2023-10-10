---
title: "Git"
layout: archive
permalink: /categories/git/
author_profile: true
sidebar:
  nav: "docs"
---


{% assign posts = site.categories.Git %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}