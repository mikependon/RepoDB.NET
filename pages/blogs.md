---
layout: default
title: BLOGS
nav_order: 13
permalink: /blogs
published: false
---

# Blogs
{: .fs-9 }

What we share to the open community!
{: .fs-6 .fw-300 }

---

{% for post in site.posts %}
<p>
    <h3 class="title"><a href="{{ post.url }}">{{ post.title }}</a></h3>
    <span class="author">
        <author>{{ post.author }}</author>
        •
        <time datetime="{{ post.date | date_to_xmlschema }}">
            {% assign date_format = site.date_format | default: "%b %-d, %Y" %}
            {{ post.date | date: date_format }}
        </time>
    </span>
</p>
{% endfor %}