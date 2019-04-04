---
layout: page
title:  Biometrics
permalink: /biometrics/
---

{% for post in site.categories.biometrics %}
  <article>
    <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
    <p>{{ post.summary }}</p>
  </article>
{% endfor %}