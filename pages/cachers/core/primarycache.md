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

A cacher class for the class primary property. It uses [PrimaryMapper](/mapper/primarymapper) internally to extract results and caches them for future use.

{: .note }
> Internally, this class is widely used within the library.

## Methods

Below are the methods available from this class.

| Flush | Allows you to flush the caches. |
| Get | Returns the list of [Field](/class/field) objects. |

## Usability

Call the `Get()` method by passing the class type:

```csharp
var primary = PrimaryCache.Get(typeof(Person));
// Use the 'primary' here
```

Or via generic type:

```csharp
var primary = PrimaryCache.Get<Person>();
// Use the 'primary' here
```

{: .note }
> Internally, it uses [PrimaryMapper](/mapper/primarymapper) and [PropertyCache](/cacher/propertycache) to extract [ClassProperty](/class/classproperty) objects and filter them using [IsPrimary()](/class/classproperty#isprimary).