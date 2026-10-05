---
layout: default
title: Home
---

<div class="intro">
  <p class="roles">Embedded Systems Engineer | Robotics Software (self-taught)</p>
  <p>I'm working toward robotics software engineering by building the layer
  between hardware and behavior — STM32 drivers, sensor fusion, and ROS2
  control stacks for robots that have to actually move. No formal program;
  everything below runs on real boards or in simulation.</p>
  <p class="cta">Interested in embedded robotics or ROS2 control architecture? Let's talk.</p>
  <div class="links">
    github: <a href="https://github.com/" target="_blank" rel="noopener">@yourhandle</a><br>
    email: <a href="mailto:you@example.dev">you [at] example [dot] dev</a>
  </div>
</div>
{% assign latest = site.posts.first %}
{% if latest %}
<div class="latest-post">
  <div class="label">LATEST FROM THE BLOG</div>
  <a href="{{ latest.url | relative_url }}" class="title">{{ latest.title }}</a>
  <span class="date"> · {{ latest.date | date: "%Y-%m-%d" }}</span>
  <p class="excerpt">{{ latest.excerpt | strip_html | truncate: 220 }}</p>
  <div class="read-more">
    <a href="{{ latest.url | relative_url }}">Read post →</a>
  </div>
</div>
{% endif %}

<section id="projects">
  <div class="grid">
    {% for project in site.projects %}
    {% if project.status == "closed" %}
    <div class="card {{ project.status }}">
    {% else %}
    <a class="card {{ project.status }}" href="{{ project.url | relative_url }}">
    {% endif %}
      <div class="card-top">
        <div class="tag {{ project.status }}">{{ project.status | upcase }}</div>
        <div class="card-title">{{ project.title }}</div>
      </div>
      <div class="card-preview">
        {% if project.image %}
          <video autoplay muted loop playsinline src="{{ project.image | relative_url }}"></video>
        {% else %}
          <span>preview</span>
        {% endif %}
      </div>
      <div class="card-body">{{ project.description | default: project.excerpt | strip_html | truncate: 750 }}</div>
      <div class="card-keywords">
        {% for tag in project.tags %}
          {% case tag %}
            {% when "ROS2" %}
              <span class="kw-ros">{{ tag }}</span>
            {% when "PID" %}
              <span class="kw-pid">{{ tag }}</span>
            {% when "VFH" %}
              <span class="kw-vfh">{{ tag }}</span>
            {% when "Local Planner" %}
              <span class="kw-localplanner">{{ tag }}</span>
            {% when "Obstacle Avoidance" %}
              <span class="kw-obsavoid">{{ tag }}</span>
            {% when "GZ" %}
              <span class="kw-gz">{{ tag }}</span>
            {% when "BT" %}
              <span class="kw-bt">{{ tag }}</span>
            {% when "Control Systems" %}
              <span class="kw-ctrlsys">{{ tag }}</span>
            {% when "Computer Vision" %}
              <span class="kw-cpv">{{ tag }}</span>
            {% else %}
             <span>{{ tag }}</span>
          {% endcase %}
        {% endfor %}
      </div>
    {% if project.status == "closed" %}
    </div>
    {% else %}
    </a>
    {% endif %}
    {% endfor %}
  </div>
</section>
