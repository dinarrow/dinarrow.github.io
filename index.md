---
layout: default

---


### Recent Posts

{% for post in site.posts %}
<li><a href="{{ post.url }}">{{ post.title}}</a>
    <span class="postDate">{{ post.date | date: "%b %-d, %Y" }}</span>
    </li>
{% endfor %}


