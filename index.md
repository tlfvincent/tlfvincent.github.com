---
layout: page
title:
tagline: Supporting tagline
---
{% include JB/setup %}

<ul class="posts">
  {% for post in site.posts %}
    <li>
       <span>{{ post.date | date_to_string }}</span> &raquo;
       <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
    </li>
       {{ post.content | strip_html | truncatewords:75}}<br>
            <a href="{{ post.url }}">Read more...</a><br><br>
  {% endfor %}
</ul>


