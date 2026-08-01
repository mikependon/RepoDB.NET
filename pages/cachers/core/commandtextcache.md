---
layout: default
sidebar: cachers
title: "CommandTextCache"
description: "A class that is being used to retrieve the cached command texts within RepoDB library."
permalink: /cacher/commandtextcache
tags: [repodb, commandtextcache]
parent: CACHERS
---

# CommandTextCache

---

A cacher class for generated SQL command texts. It provides internal caching for SQL statements, allowing the library to reuse previously generated statements for faster execution.

## Methods

This class exposes a single method, `Flush()`, which clears all cached command texts.

## Usability

Call `Flush()` only when you need to force regeneration of cached SQL statements.

{: .warning }
> Avoid using this class in application code unless regeneration of SQL statements is explicitly required.