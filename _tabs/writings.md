---
layout: default
icon: fas fa-pencil-square
order: 3
---

# Writings
---

## Books
- Management Engineering
- Being a Better Professional

## Blogs
  <ul>
    {% for post in site.posts %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>

## Quora Answers

  - [132 Questions](https://www.quora.com/profile/Kumar-Bhot/answers){:target="_blank"}
