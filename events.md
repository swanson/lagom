---
layout: default
title: Events
nav: main
permalink: /events/
---

<div class="main-list">

  <h1 class="page-heading">Our Events</h1>

  <ul class="post-list">
    {% for event in site.categories.event %}
      <li>
        <span class="post-meta">{{ event.date | date: "%B %-d, %Y" }} &mdash; {{ event.guest }} </span>

        <h2>
          <a class="post-link" href="{{ event.url | prepend: site.baseurl }}">{{ event.title }}</a>
        </h2>
      </li>
    {% endfor %}
  </ul>

</div>
