---
layout: page
title:  Mobile Programming
permalink: /mobile/
---

<ul class="post-list">
{% for post in site.categories.mobile %}
  <li>
    <article>
      <span class="post-meta">{{ post.date | date: "%b %d, %Y" }}</span>
      <h3><a href="{{ post.url }}" class="post-link">{{ post.title }}</a></h3>
      <p>{{ post.summary }}</p>
    </article>
  </li>
{% endfor %}
</ul>