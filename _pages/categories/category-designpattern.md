---
title: "디자인 패턴"
layout: archive
permalink: /categories/designpattern/
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.DESIGNPATTERN %}
{% for post in posts %} {% include archive-category.html type=page.entries_layout %} {% endfor %}
