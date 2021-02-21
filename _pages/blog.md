---
title: ""
permalink: /blog/
author_profile: false
---

[//]: # <div class="all-tags">
[//]: # <style>
[//]: # div.sticky {
[//]: #   position: -webkit-sticky;
[//]: #   position: sticky;
[//]: #   top: 0;
[//]: #   padding: 5px;
[//]: # }
[//]: # </style>
[//]: # 
[//]: # <div class="sticky">
[//]: #   Tags:
[//]: #   {% assign all_tags = '' | split: ',' %}
[//]: #   
[//]: #    {% for post in site.posts %}
[//]: #       {% for tags in post.tags %}
[//]: #           {% for tag in tags %}
[//]: #               {% assign all_tags = all_tags | push: tag %}
[//]: #           {% endfor %}
[//]: #       {% endfor %}
[//]: #   {% endfor %}
[//]: #   
[//]: #   {% assign all_tags = all_tags | sort %}
[//]: #   {% assign all_tags = all_tags | uniq %}
[//]: #   
[//]: #   <ul class="tag-list">
[//]: #       {% for tag in all_tags %}
[//]: #           <li><a href="{{ site.tag_dir | prepend: '/' }}/{{ tag | uri_escape }}">{{ tag }}</a></li>
[//]: #       {% endfor %}
[//]: #   
[//]: #   </ul>
[//]: # </div>
[//]: # </div>

<ul>
  {% for post in site.posts %}
    <li>
    {{ post.date | date: "%B %-d, %Y" }}:  <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>


