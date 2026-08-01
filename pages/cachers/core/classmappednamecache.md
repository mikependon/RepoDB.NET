---
layout: default
sidebar: cachers
title: "ClassMappedNameCache"
description: "A class that is being used to retrieve the cached name of the class or data entity."
permalink: /cacher/classmappednamecache
tags: [repodb, classmappednamecache]
parent: CACHERS
---

# ClassMappedNameCache

---

A cacher class for class name mappings. It uses [ClassMapper](/mapper/classmapper) internally to extract results, then caches them for subsequent use, providing a second caching layer for class name mapping extraction.

{: .note }
> Internally, this class is widely used within the library.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Flush | Allows you to flush the caches. |
| Get | Returns the mapped name of the class. |

## Usability

Call the `Get()` method by passing the class type:

```csharp
var mappedName = ClassMappedNameCache.Get(typeof(Person));
// Use the 'mappedName' here
```

Or via generic type:

```csharp
var mappedName = ClassMappedNameCache.Get<Person>();
// Use the 'mappedName' here
```

{: .note }
> The extraction checks for the [Map](/attribute/map#class-mapping) attribute first, then falls back to implicit mapping, and finally uses `typeof(Class).Name` from `System.Reflection`.