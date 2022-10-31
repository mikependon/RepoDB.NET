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

A mapper class that is being used to map the class into its equivalent object in the database (i.e.: Table, View). This class is used as an alternative to the [Map](/attribute/map) attribute.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Add | Adds a mapping between the class and the name of the database object. |
| Clear | Clears all the existing database object name mappings. |
| Get | Gets the existing mapped database object name of the class. |
| Remove | Removes the exising database object name of the class. |

## Usability

To add a mapping, simply call the `Add()` method.

```csharp
ClassMapper.Add<Customer>("[sales].[Customer]", true);
```

{: .warning }
> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get()` method.

```csharp
var mappedName = ClassMapper.Get<Customer>();
```

{: .note }
> Please consider to always use the [ClassMappedNameCache](/cacher/classmappednamecache) class when extracting the mapped class name.

To remove the mapping, use the `Remove()` method.

```csharp
ClassMapper.Remove<Customer>();
```