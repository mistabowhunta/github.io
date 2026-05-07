---
layout: post
title: NasonNation Robotics Blog
weight: 3
tags: [vr nason nation, robotics, mechatronics, pico 4, aegis control, project exes, robotic hand, borider, hand cannon, autonomous uav, systems]
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
