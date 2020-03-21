---
layout: page
title: "ClassMappedNameCache (RepoDb)"
permalink: /cacher/classmappednamecache
tags: [repodb, class, classmappednamecache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## ClassMappedNameCache

A cacher class for the class name mapping. It provides a 2nd-layer caching for the library when it comes to the class name mapping extraction. As a result, the library is fast-enough when reusing the already extracted class mapped name on any execution.

> Internally, this class is widely used within the library.

#### Methods

Below are the methods available from this class.

- Flush - allows you to flush the caches.
- Get - returns the list of [Field](/class/field) objects.

#### Use-Cases

You only use this class if you would like to get the mapped name of the class in general purpose.

> You should rely on this class rather using the `nameof(Class)` of C# when working against the class name.

#### How to Use?

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

> The extraction is first checking the presence of the [Map](/attribute/map) attribute and extract the name-mapping from there, otherwise, it will use the `typeof(Class).Name` of the `System.Reflection`.