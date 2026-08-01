---
layout: default
sidebar: mappers
title: "ClassMapper"
description: "A mapper class that is being used to map the class into its equivalent object in the database (i.e.: Table, View). This class is used as an alternative to Map attribute."
permalink: /mapper/classmapper
tags: [repodb, classmapper]
parent: MAPPERS
---

# ClassMapper

---

A mapper class for associating a class with its corresponding database object (table or view). This is the programmatic alternative to the [Map](/attribute/map) attribute.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Add | Adds a mapping between the class and the name of the database object. |
| Clear | Clears all the existing database object name mappings. |
| Get | Gets the existing mapped database object name of the class. |
| Remove | Removes the exising database object name of the class. |

## Usability

To add a mapping, call the `Add()` method:

```csharp
ClassMapper.Add<Customer>("[sales].[Customer]", true);
```

{: .warning }
> An exception is thrown if a mapping already exists and `false` is passed for the `force` argument.

To retrieve the mapping, use the `Get()` method:

```csharp
var mappedName = ClassMapper.Get<Customer>();
```

{: .note }
> Always use [ClassMappedNameCache](/cacher/classmappednamecache) when retrieving the mapped class name.

To remove the mapping, use the `Remove()` method:

```csharp
ClassMapper.Remove<Customer>();
```