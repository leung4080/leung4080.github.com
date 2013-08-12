---
layout: page
title:  本站简介 
tagline: 
---
{% include JB/setup %}


本站用于分享一下个人的Linux学习经验，有趣实用的软件等。


##文章列表##
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

