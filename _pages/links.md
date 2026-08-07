---
layout: page
title: Links
permalink: /links/
description: Friends and websites worth visiting.
nav: true
nav_order: 5
---

{% for section in site.data.links %}
<section{% unless forloop.first %} class="mt-5"{% endunless %}>

<h2 class="mb-3">{{ section.title }}</h2>

    <div class="row row-cols-1 row-cols-md-2">
      {% for item in section.links %}
        <div class="col mb-4">
          <a href="{{ item.url }}" target="_blank" rel="noopener noreferrer" aria-label="访问 {{ item.name }} 的网站" style="color: inherit; text-decoration: none">
            <article class="card h-100 hoverable">
              <div class="card-body" style="display: grid; grid-template-columns: auto minmax(0, 1fr); gap: 1.5rem; align-items: center">
                {% if item.avatar %}
                  <img
                    src="{{ item.avatar | relative_url }}"
                    alt="{{ item.name }} 的头像"
                    class="rounded-circle flex-shrink-0"
                    width="88"
                    height="88"
                    loading="lazy"
                    decoding="async"
                    style="object-fit: cover"
                  >
                {% else %}
                  <span
                    class="rounded-circle d-flex align-items-center justify-content-center flex-shrink-0"
                    role="img"
                    aria-label="{{ item.name }} 的站点标识"
                    style="width: 88px; height: 88px; background: var(--global-theme-color); color: var(--global-bg-color); font-size: 2rem; font-weight: 600"
                  >{{ item.avatar_text }}</span>
                {% endif %}
                <div style="min-width: 0; overflow-wrap: anywhere">
                  <h3 class="card-title h4 mb-1">{{ item.name }}</h3>
                  <p class="card-text mb-1">{{ item.description }}</p>
                  <span>{{ item.display_url }} ↗</span>
                </div>
              </div>
            </article>
          </a>
        </div>
      {% endfor %}
    </div>

  </section>
{% endfor %}
