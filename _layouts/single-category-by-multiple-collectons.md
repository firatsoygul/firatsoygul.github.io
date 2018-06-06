---
layout: archive
---
<!-- Bir tek koleksiyon altında, bütün alt kategorilerini gösterir-->
{{ content }}

{% capture written_label %}'None'{% endcapture %}

{% for collection in site.collections %}
  {% unless collection.output == false or collection.label == "posts" %}
    {% if collection.label == page.collection %}
      
      {% capture label %}{{ collection.label }}{% endcapture %}
      {% if label != written_label %}
        <h2 id="{{ label | slugify }}" class="archive__subtitle">    <!-- {{ label }} -->   </h2>
        {% capture written_label %}{{ label }}{% endcapture %}
      {% endif %}
    
    {% for post in collection.docs %}
      {% assign categories = post.categories %}
      {% for category in categories %}
      {% capture category_label %}{{ category }}{% endcapture %}
        {% if category_label != written_label %}
        <h2 id="{{ category_label | slugify }}" class="archive__second__subtitle">{{ category_label }}</h2>
        {% capture written_label %}{{ category_label }}{% endcapture %}
        {% endif %}
          {% include archive-single.html %}
      {% endfor %}
    {% endfor %}
    {% endif %}
  {% endunless %} 
{% endfor %}