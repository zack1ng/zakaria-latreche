---
layout: default
title: Home
---

<div class="intro">
  <h1 class="name">Zakaria Latreche</h1>
  <p class="roles">Mechatronics Engineer | Robotics & Embedded Systems</p>
  <p class="desc">Electronics and robotics enthusiast with a strong passion for bringing ideas to life. Currently pursuing a Master’s degree in
Advanced Mechatronics at Polytech Annecy-Chambéry, I’ve developed solid expertise in PCB design, embedded systems
programming, and autonomous robot development using ROS. What truly drives me is transforming concepts into functional
systemswhether it’s a competitive RoboCup robot or a miniaturized IoT sensor. I particularly enjoy challenges that require
balancing hardware and software skills, and I thrive in collaborative projects where creativity meets technical rigor.</p>
  <p class="cta"></p>
  <div class="links">
    github: <a href="https://github.com/" target="zack1ng" rel="noopener">@zack1ng</a><br>
    email: <a href="mailto:zakaria.latreche@etu.univ-smb.fr">zakaria.latreche [at] etu.univ-smb [dot] fr</a>
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
