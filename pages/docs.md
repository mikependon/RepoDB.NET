---
layout: default
title: DOCS
description: "RepoDB is a production-ready data access platform for .NET applications. It is the best alternative ORM to both Dapper and EntityFramework."
nav_order: 1
permalink: /docs
nav_exclude: true
---

# Docs
{: .fs-9 }

Navigate the RepoDB documentation.
{: .fs-6 .fw-300 }

---

{% for doc in site.data.documentation.docs %}
{% if doc.isdoc == true %}

### {{ doc.title }}

<ul>
    {% for topic in doc.topics %}
    <li><a href="{{ topic.url }}">{{ topic.title }}</a></li>
    {% endfor %}
</ul>

{% endif %}
{% endfor %}
