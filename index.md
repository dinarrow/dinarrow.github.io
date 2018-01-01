---
layout: default

---


### Recent Posts

{% for post in paginator.posts %}
<li><a href="{{ post.url }}">{{ post.title}}</a>
    <span class="postDate">{{ post.date | date: "%b %-d, %Y" }}</span>
    </li>
{% endfor %}


