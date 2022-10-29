---
layout: default
sidebar: cachers
title: "PrimaryCache"
description: "A class that is being used to retrieve the cached primary field of the class or data entity."
permalink: /cacher/primarycache
tags: [repodb, primarycache]
parent: CACHERS
---

# PrimaryCache

---

A cacher class for the class primary property. Underneath, it uses the [PrimaryMapper](/mapper/primarymapper) class to extract the results and caching it for future use.

> Internally, this class is widely used within the library.

## Methods

Below are the methods available from this class.

| Flush | Allows you to flush the caches. |
| Get | Returns the list of [Field](/class/field) objects. |

## Usability

You can simply call the `Get()` method of this class by passing the class type.

```csharp
var primary = PrimaryCache.Get(typeof(Person));
// Use the 'primary' here
```

Or, via generic type.

```csharp
var primary = PrimaryCache.Get<Person>();
// Use the 'primary' here
```

> Internally, it uses the [PrimaryMapper](/mapper/primarymapper) and [PropertyCache](/cacher/propertycache) classes to extract the list of [ClassProperty](/class/classproperty) and filter the result using the given [IsPrimary()](/class/classproperty#isprimary) method.