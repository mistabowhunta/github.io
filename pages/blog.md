---
layout: post
title: Blog
weight: 3
tags: vr
permalink: /posts/
---

# NasonNation Robotics Blog

<ul>
  {% for post in site.posts %}
    <li>
      <span>{{ post.date | date: "%B %d, %Y" }}</span> &raquo; 
      <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
