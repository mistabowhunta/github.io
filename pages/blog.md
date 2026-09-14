---
layout: default
title: Blog
weight: 3
permalink: /blog/
---

<h1 class="mt-5 mb-4">NasonNation Robotics Blog</h1>
<p class="mb-4">Technical deep-dives, hardware troubleshooting, and software workarounds from the workbench</p>

<ul>
  {% for post in site.posts %}
    <li>
      {{ post.date | date: "%B %d, %Y" }} »
      [{{ post.title }}]({{ site.baseurl }}{{ post.url }})
      {{ post.description }}
    </li>
  {% endfor %}
</ul>
