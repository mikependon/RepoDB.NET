---
layout: default
sidebar: cachers
title: "PropertyCache"
description: "A class that is being used to retrieve the cached properties of the class or data entity."
permalink: /cacher/propertycache
tags: [repodb, propertycache]
parent: CACHERS
---

# PropertyCache

---

A widely-used cacher class for [ClassProperty](/class/classproperty) objects. It provides a second caching layer for class property extraction.

{: .note }
> Internally, this class is widely used within the library.

## Methods

Below are the methods available from this class.

| Flush | Allows you to flush the caches. |
| Get | Returns the list of [ClassProperty](/class/classproperty) objects. |

## Usability

Call the `Get()` method by passing the class type:

```csharp
var properties = PropertyCache.Get(typeof(Person));
// Use the 'properties' here
```

Or via generic type:

```csharp
var properties = PropertyCache.Get<Person>();
// Use the 'properties' here
```

{: .note }
> Internally, it uses [PropertyMapper](/mapper/propertymapper) and [ClassExpression](/class/classexpression) to extract and cache [ClassProperty](/class/classproperty) objects.