---
layout: default
title: Home
---

<style>
.home {
  max-width: 650px;
  margin: 2rem auto;
  font-family: Arial, sans-serif;
  line-height: 1.6;
}
.home h1 {
  text-align: center;
}
</style>

<div class="home">
  <h1>Welcome to the Trading Blog</h1>
  <p>This site documents small trading experiments built with Python. The aim is to keep notes on simple strategies and share code snippets that can be expanded later.</p>

  <h2>Recent Posts</h2>
  <ul>
    {% for post in site.posts limit:5 %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> <small>{{ post.date | date: "%b %d, %Y" }}</small></li>
    {% endfor %}
  </ul>

  <h2>About</h2>
  <p>Notebooks used for exploration live in the <a href="{{ '/notebooks/' | relative_url }}">notebooks</a> folder. Posts summarise ideas such as the <a href="{{ '/2025/06/03/moving-average-crossover.html' | relative_url }}">moving average crossover strategy</a>.</p>
</div>
