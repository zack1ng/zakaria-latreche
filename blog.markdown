---
layout: default
title: Blog
permalink: /blog/
---

<div class="section-head">
  <h2>Blog</h2>
  <span class="count">{{ site.posts.size }}</span>
</div>

{% for post in site.posts %}
<div style="border-bottom: 1px solid var(--rule); padding: 18px 0;">
  <a href="{{ post.url | relative_url }}" style="font-size:16px; font-weight:600;">{{ post.title }}</a>
  <div style="font-size:12px; color:var(--ink-soft); margin: 4px 0 8px;">{{ post.date | date: "%Y-%m-%d" }}</div>
  <div style="font-size:14px; color:var(--ink-soft);">{{ post.excerpt | strip_html | truncate: 160 }}</div>
</div>
{% endfor %}
