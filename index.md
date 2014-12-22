---
layout: style
title: index
---

<div id="latest">
    <ul>
        {% for post in site.posts %}
        <li><span class="date">{{ post.date | date: "%Y/%m/%d" }}</span><br/><a href="{{ post.url }}">{{ post.title }}</a></li>
        {% endfor %}
    </ul>
</div>
