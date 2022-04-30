---
layout: default
title: blog
---

Welcome to the official [mini-lang](https://www.mini-lang.org) blog!
This is a place where you can read about mini-lang, learn about the language, and the community around it.

Below is a list of the most recent posts:

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>