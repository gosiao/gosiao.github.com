---
layout: archive
title: ""
permalink: /blog/
author_profile: false
---

<div class="page">
  <div class="page__content">
      {% for post in site.posts %}
          <h4>
             {{ post.date | date: "%b %e, %Y" }}
             <strong><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></strong>
          </h4>
      {% endfor %}
  </div>
</div>


