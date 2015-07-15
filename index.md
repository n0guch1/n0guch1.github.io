---
layout: style
title: Archives
---

<div id="latest">
    <ul>
        {% for post in site.posts %}
        <li><span class="date">{{ post.date | date: "%Y/%m/%d" }}</span><br/><a href="{{ post.url }}">{{ post.title }}</a></li>
        <p class="summary">{{post.summary}}</p>
        {% endfor %}
    </ul>
</div>

<!--  カテゴリー取得-->
<!-- <div>
  <h4>iOSの記事一覧</h4>
  <ul>
  {% for post in site.categories.ios %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
  </ul>
  <h4>Androidの記事一覧</h4>
  <ul>
  {% for post in site.categories.android %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
  </ul>
</div> -->
