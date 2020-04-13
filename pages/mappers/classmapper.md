---
layout: navpage
sidebar: mappers
title: "ClassMapper (RepoDb)"
permalink: /mapper/classmapper
tags: [repodb, class, classmapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# ClassMapper

A mapper class that is used to map the class into its equivalent object in the database (ie: Table, View). This class is used as an alternative to [Map](/attribute/map) attribute.

#### Methods

Below are the methods available from this class.

- `Add` - adds a mapping between the entity model and database object.
- `Clear` - clears all the entity model and database object mappings.
- `Get` - gets the mapped name of the entity model.
- `Remove` - removes the exising mapping between the entity model identity and database object.

#### Use-Cases

You should use this class if you do not like to use the [Map](/attribute/map) attribute. Usually, the purpose of the usability is to make sure that the model is attribute-free and is not bound to a specific ORM.

#### How to Use?

To add a mapping, simply call the `Add` method.

```csharp
ClassMapper.Add<Customer>("[sales].[Customer]", true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get` method.

```csharp
var mappedName = ClassMapper.Get<Customer>();
```

> Please consider to always use the [ClassMappedNameCache](/cacher/classmappednamecache) class when extracting the mapped class name.

To remove the mapping, use the `Remove` method.

```csharp
ClassMapper.Remove<Customer>();
```