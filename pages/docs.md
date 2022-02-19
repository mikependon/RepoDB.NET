---
layout: default
title: DOCS
description: "RepoDB is a hybrid-ORM library for .NET. It is the best alternative ORM to both Dapper and EntityFramework."
nav_order: 1
permalink: /docs
---

# Docs
{: .fs-9 }

Help you navigate to learn RepoDB!
{: .fs-6 .fw-300 }

---

Below are the items you will learn with RepoDB.

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
