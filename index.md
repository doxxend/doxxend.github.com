---
layout: page
title: Home
tabify: h2
tagline: 
---
{% include JB/setup %}


### 公告板
---
#### 这个网站负责存放我的[文章](archive.html)以及我认为值得分享的[资源](pages/wiki.html)。

<br />



<br />

### 最新文章
---
<!--- ALTERNATIVE TO SHOW POSTS
{% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span>  : <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
-->

{% assign posts = site.posts %}
{% assign listing_limit = 5 %}
{% include post-listing.html %}


