---
layout: archive
author_profile: true
---
&#217;% include base_path %}

&LT;h3 class="archive__subtitle">Recent Posts&LT;/h3>

&#217;% for post in paginator.posts %}
&#217;% include archive-single.html %}
&#217;% endfor %}

&#217;% include paginator.html %}
