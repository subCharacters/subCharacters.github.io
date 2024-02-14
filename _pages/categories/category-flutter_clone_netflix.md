---
title: "Flutter netflix clone coding"
layout: archive
permalink: /categories/flutter_clone_netflix/
author_profile: true
sidebar:
  nav: "docs"
---


{% assign posts = site.categories.Flutter_clone_netflix %}
{% if posts.size > 0 %}
  {% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
{% else %}
  <p>nothing</p>
{% endif %}