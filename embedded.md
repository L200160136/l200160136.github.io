---
layout: page
title:  Embedded
permalink: /embedded/
---

{% for post in site.categories.embedded %}
  <article>
    <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
    <p>{{ post.summary }}</p>
  </article>
{% endfor %}