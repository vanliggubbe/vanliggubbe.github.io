---
layout: default
title: Home
---


<!--
{% assign about = site.pages | where: "url", "/about.html" | first %}
## [Hi!]({{ about.url | relative_url }})
{{ about.excerpt }}
-->

{% for post in site.posts %}
## [{{ post.title }}]({{ post.url | relative_url }}) — {{ post.date | date: "%Y-%m-%d" }}
{{ post.excerpt }}
[More...]({{ post.url | relative_url}})
{% endfor %}

