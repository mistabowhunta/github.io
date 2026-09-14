---
layout: default
title: Blog
weight: 3
permalink: /blog/
style: fill
color: primary
description: Write post description here, or it will be the first 25 words of the post's body.
---

<h1 class="mt-5 mb-4">NasonNation Robotics Blog</h1>
<p class="mb-4">Technical deep-dives, hardware troubleshooting, and software workarounds from the workbench</p>

<ul>

  {% for post in site.posts %}
    <li>
      <span>{{ post.date | date: "%B %d, %Y" }}</span> &raquo; 
      <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
      <p>{{ post.description }}</p>
    </li>
  {% endfor %}

</ul>

<!--  
---
title: Blog
style: fill / border (choose one only)
color: primary / secondary / success / danger / warning / info / light / dark (choose one only)
description: Write post description here, or it will be the first 25 words of the post's body.
---
-->

