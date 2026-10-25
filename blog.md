---
layout: page
title: Blog
permalink: /blog
---

# The `mini` blog

Welcome to the official [mini-lang](https://www.mini-lang.org) blog!
This is a place where you can read about mini-lang, learn about the language, and the community around it.

For the current implementation, read [From syntax to working control flow](/tutorial/v1).
Its examples are checked against the educational v1 interpreter. The original
chapters below provide background rather than a complete language specification.

## Recent posts

<ul>
  {% for post in site.posts %}
    <li style="margin-bottom: 15px;">
      <h2 style="margin-bottom: 0px;"><a href="{{ post.url }}">{{ post.title }}</a></h2>
      {{ post.summary }}
      <!-- tags -->
      <div style="margin-top: 5px;">
        {% for tag in post.tags %}
          <span style="color: #999; margin-right: 5px;">{{ tag }}</span>
        {% endfor %}
      </div>
    </li>
  {% endfor %}
</ul>

<!--
## Categories

<ul>
  {% for category in site.categories %}
    <li style="margin-bottom: 15px;">
      <h2 style="margin-bottom: 0px;"><a href="{{ category.url }}">{{ category.title }}</a></h2>
      {{ category.description }}
    </li>
  {% endfor %}
</ul>
-->
