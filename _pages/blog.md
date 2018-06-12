---
permalink: /blog/
layout: splash
---

 <!-- Son yazılar-->
<div class="list__item_border_posts">
    <div class="archive_subtitle_container" >
  <span class="archive__subtitle_span" style="display:inline-block;">
    <h3 class="archive__subtitle">{{ site.data.ui-text[site.locale].recent_posts | default: "Recent Posts" }}</h3>
  </span>
  {% assign of_collection = "posts" %}
  <span class="archive__subtitle_tag">{% include tag-list-collection.html %}</span>
  </div>
  {% for post in site.posts %}
    {% include archive-with-image.html item_teaser="yes" item_collection=of_collection %}
  {% endfor %}
</div>