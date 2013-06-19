---
layout: default
title: "Posts on Security"
comments: false
sharing: false
footer: true
---

{% include header.html %}
<div class="blog-index">
  {% assign index = true %}
  {% for post in site.categories.security %}
  {% assign content = post.content %}
    <article>
      {% include article.html %}
    </article>
    {% unless post == paginator.posts.last %}
    <hr>
    {% endunless %}
  {% endfor %}
  <div class="pagination">
    {% if paginator.next_page %}
    <a class="prev" href="{{paginator.next_page}}">&larr; Older</a>
    {% endif %}

    {% if paginator.previous_page %}
    <a class="next" href="{{paginator.previous_page}}">Newer &rarr;</a>
    {% endif %}
  </div>
</div>

