---
layout: navpage
sidebar: cachers
title: "PropertyMappedNameCache (RepoDb)"
permalink: /cacher/propertymappednamecache
tags: [repodb, class, propertymappednamecache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# PropertyMappedNameCache

A cacher class for the `System.Reflection.PropertyInfo`. It provides a 2nd-layer caching for the library when it comes to the property name mapping extraction. As a result, the library is fast-enough when reusing the already extracted property mapped name on any execution.

#### Methods

Below are the methods available from this class.

- `Flush` - allows you to flush the caches.
- `Get` - returns the mapped name of the property.

#### Use-Cases

You should use this class if you would like to get the mapped name of the property in general purpose.

> You should rely on this class rather using the `nameof(Class.Property)` of C# when working against the property name.

#### How to Use?

You can simply call the `Get()` method of this class by passing the instance of `System.Reflection.PropertyInfo`.

```csharp
var properties = PropertyCache.Get<Person>();
properties
    .AsList()
    .ForEach(p =>
    {
        var mappedName = PropertyMappedNameCache.Get(p);
        // Use the 'mappedName' here
    })
```

> The extraction is first checking the presence of the [Map](/attribute/map#property-mapping) attribute and extract the name-mapping from there, otherwise, it will use the `PropertyInfo.Name` of the `System.Reflection`.