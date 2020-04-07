---
layout: smallpage
title: Blogs (RepoDb)
permalink: /blogs
---

<div class="blogs-container">
    <h1 class="blog-title">Blogs</h1>
    <ul class="blogs">
    {% for post in site.posts %}
        <li class="blog-entry">
            <h3 class="title"><a href="{{ post.url }}">{{ post.title }}</a></h3>
            <span class="author">
                <author>{{ post.author }}</author>
                •
                <time datetime="{{ post.date | date_to_xmlschema }}">
                    {% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
                    {{ post.date | date: date_format }}
                </time>
            </span>
        </li>
    {% endfor %}
    </ul>
</div>