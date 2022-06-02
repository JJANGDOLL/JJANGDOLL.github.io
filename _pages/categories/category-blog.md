---
title: "블로그 커스터마이즈"
layout: archive
permalink: /categories/blog/
author_profile: true
sidebar_main: true
---

[mmistakes](https://github.com/mmistakes/minimal-mistakes) 테마 커스터마이즈

{% assign posts = site.categories.BLOG %}
{% for post in posts %} {% include archive-category.html type=page.entries_layout %} {% endfor %}
