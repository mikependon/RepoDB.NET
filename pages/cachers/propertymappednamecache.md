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

A cacher class for the `System.Reflection.PropertyInfo`. Underneath, it uses the [PropertyMapper](/mapper/propertymapper) class to extract the results and caching it for future use.

> You should rely on this class rather using the `nameof(Class.Property)` of C# when working against the property name.

## Methods

Below are the methods available from this class.

| Flush | Allows you to flush the caches. |
| Get | Returns the mapped name of the property. |

## Usability

You can simply call the `Get()` method of this class by passing the instance of `System.Reflection.PropertyInfo`.

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

Or via expression.

```csharp
var mappedName = PropertyCache.Get<Person>(e => e.FirstName);
// Use the 'mappedName' here
```

> The extraction is first checking the presence of the [Map](/attribute/map#property-mapping) attribute and extract the name-mapping from there, then checks the implicit-mapping, otherwise, it will use the `PropertyInfo.Name` of the `System.Reflection`.
