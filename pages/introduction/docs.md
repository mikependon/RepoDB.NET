---
layout: navpage
sidebar: introduction
title: Docs
description: "RepoDB is a hybrid-ORM library for .NET. It is the best alternative ORM to both Dapper and EntityFramework."
permalink: /docs
---

# Docs

Below are the list of the things you can learn with RepoDb. You can also click the links provided in the left sidebar.

{% for doc in site.data.documentation.docs %}
{% if doc.isdoc == true %}

#### {{ doc.title }}

{% if doc.description != null %}
{{ doc.description }}
{% endif %}

<ul>
    {% for topic in doc.topics %}
    <li><a href="{{ topic.url }}">{{ topic.title }}</a></li>
    {% endfor %}
</ul>

{% endif %}
{% endfor %}
