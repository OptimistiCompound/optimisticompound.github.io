---
layout: page
title: Links
permalink: /links/
description: Friends and websites worth visiting.
nav: true
nav_order: 5
---

这里收集朋友们的个人网站与值得常去的互联网角落。

<div class="row row-cols-1 row-cols-md-2">
{% for friend in site.data.links %}
  <div class="col mb-4">
    <a href="{{ friend.url }}" target="_blank" rel="noopener noreferrer" aria-label="访问 {{ friend.name }} 的网站" style="color: inherit; text-decoration: none">
      <article class="card h-100 hoverable">
        <div class="card-body d-flex align-items-center">
          <img
            src="{{ friend.avatar | relative_url }}"
            alt="{{ friend.name }} 的头像"
            class="rounded-circle"
            width="88"
            height="88"
            loading="lazy"
            decoding="async"
            style="object-fit: cover"
          >
          <div class="ml-4">
            <h2 class="card-title h4 mb-1">{{ friend.name }}</h2>
            <p class="card-text mb-1">{{ friend.description }}</p>
            <span>{{ friend.display_url }} ↗</span>
          </div>
        </div>
      </article>
    </a>
  </div>
{% endfor %}
</div>
