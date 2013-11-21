---
title: Talks
layout: default
---

{% for post in site.categories.talks %}
  [{{ post.title }}]({{ page.url }})
{% endfor %}
