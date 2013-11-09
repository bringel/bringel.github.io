---
layout: default
title: Blog Archive
---

{% for post in site.posts %}

[{{ post.title }}]({{ post.url }}) - {{ post.date | date_to_long_string }}

{% endfor %}