---
layout: archive
permalink: /projects/
title: "Projects"
author_profile: true
---
.li {
  font-size: $type-size-5;
}


<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
