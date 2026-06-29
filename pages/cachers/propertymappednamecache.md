---
layout: default
sidebar: cachers
title: "PropertyMappedNameCache"
description: "A class that is being used to retrieve the cached name of the class or data entity property."
permalink: /cacher/propertymappednamecache
tags: [repodb, propertymappednamecache]
parent: CACHERS
---

# PropertyMappedNameCache

---

A cacher class for `System.Reflection.PropertyInfo` mapped names. It uses [PropertyMapper](/mapper/propertymapper) internally to extract results and caches them for future use.

{: .important }
> Use this class instead of the C# `nameof(Class.Property)` expression when working with property names.

## Methods

Below are the methods available from this class.

| Flush | Allows you to flush the caches. |
| Get | Returns the mapped name of the property. |

## Usability

Call the `Get()` method by passing a `System.Reflection.PropertyInfo` instance:

```csharp
var properties = PropertyCache.Get<Person>();
properties
    .AsList()
    .ForEach(p =>
    {
        var mappedName = PropertyMappedNameCache.Get(p.PropertyInfo);
        // Use the 'mappedName' here
    })
```

Or via expression:

```csharp
var mappedName = PropertyCache.Get<Person>(e => e.FirstName);
// Use the 'mappedName' here
```

{: .important }
> The extraction checks for the [Map](/attribute/map#property-mapping) attribute first, then falls back to implicit mapping, and finally uses `PropertyInfo.Name` from `System.Reflection`.
