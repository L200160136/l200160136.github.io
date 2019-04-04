---
layout: page
title:  Embedded
permalink: /embedded/
---

<ul class="post-list">
{% for post in site.categories.embedded %}
  <li>
    <article>
      <span class="post-meta">{{ post.date | date: "%m %d, %Y" }}</span>
      <h3><a href="{{ post.url }}" class="post-link">{{ post.title }}</a></h3>
      <p>{{ post.summary }}</p>
    </article>
  </li>
{% endfor %}
</ul>