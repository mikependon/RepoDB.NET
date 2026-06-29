---
layout: default
sidebar: cachers
title: "FieldCache"
description: "A class that is being used to retrieve the cached fields or properties of the class or data entity."
permalink: /cacher/fieldcache
tags: [repodb, fieldcache]
parent: CACHERS
---

# FieldCache

---

A cacher class for class properties represented as fields. It provides a second caching layer for property extraction.

{: .note }
> Internally, this class is widely used within the library.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Flush | Allows you to flush the caches. |
| Get | Returns the list of [Field](/class/field) objects. |

## Usability

Call the `Get()` method by passing the class type:

```csharp
var fields = FieldCache.Get(typeof(Person));
// Use the 'fields' here
```

Or via generic type:

```csharp
var fields = FieldCache.Get<Person>();
// Use the 'fields' here
```

{: .note }
> Internally, it uses [PropertyCache](/cacher/propertycache) to extract [ClassProperty](/class/classproperty) objects and convert them to an `Enumerable<Field>`.