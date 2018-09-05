---
layout: archive
permalink: /projects/
title: "Projects"
author_profile: true

---
  <ul>
    {% for post in site.posts %}
      <li>
        <small> <a href="{{ post.url }}">{{ post.title }}</a>
    {% endfor %}
