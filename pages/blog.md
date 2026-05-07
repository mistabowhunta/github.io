---
layout: default
title: Blog
weight: 3
tags: [vr nason nation, robotics, mechatronics, pico 4, aegis control, project exes, robotic hand, borider, hand cannon, autonomous uav, systems]
permalink: /posts/
---

<h1 class="mt-5 mb-4">NasonNation Robotics Blog</h1>

<ul>
  {% for post in site.posts %}
    <li>
      <span>{{ post.date | date: "%B %d, %Y" }}</span> &raquo; 
      <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
