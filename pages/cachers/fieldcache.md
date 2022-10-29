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

A cacher class for the class properties (as fields). It provides a 2nd-layer caching for the library when it comes to the class properties extraction (as fields).

> Internally, this class is widely used within the library.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Flush | Allows you to flush the caches. |
| Get | Returns the list of [Field](/class/field) objects. |

## Usability

You can simply call the `Get()` method of this class by passing the class type.

```csharp
var fields = FieldCache.Get(typeof(Person));
// Use the 'fields' here
```

Or, via generic type.

```csharp
var fields = FieldCache.Get<Person>();
// Use the 'fields' here
```

> Internally, it uses the [PropertyCache](/cacher/propertycache) class to extract the list of [ClassProperty](/class/classproperty) and convert and cache it all as an `Enumerable<Field>`.