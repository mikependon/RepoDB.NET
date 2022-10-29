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

A cacher class for the class name mapping. Underneath, it uses the [ClassMapper](/mapper/classmapper) class to extract the results and caching it for future use. It provides a 2nd-layer caching for the library when it comes to the class name mapping extraction.

> Internally, this class is widely used within the library.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Flush | Allows you to flush the caches. |
| Get | Returns the mapped name of the class. |

## Usability

You can simply call the `Get()` method of this class by passing the class type.

```csharp
var mappedName = ClassMappedNameCache.Get(typeof(Person));
// Use the 'mappedName' here
```

Or, via generic type.

```csharp
var mappedName = ClassMappedNameCache.Get<Person>();
// Use the 'mappedName' here
```

> The extraction is first checking the presence of the [Map](/attribute/map#class-mapping) attribute and extract the name-mapping from there, then checks the implicit-mapping, otherwise, it will use the `typeof(Class).Name` of the `System.Reflection`.