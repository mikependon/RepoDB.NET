---
layout: default
sidebar: mappers
title: "PropertyMapper"
description: "A mapper class that is used to map the class property into its equivalent column in the database. This class is used as an alternative to Map attribute."
permalink: /mapper/propertymapper
tags: [repodb, class, propertymapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Mappers
---

# PropertyMapper

---

A mapper class that is used to map the class property into its equivalent column in the database. This class is used as an alternative to [Map](/attribute/map) attribute.

### Methods

Below are the methods available from this class.

- `Add` - adds a mapping between the class property and database column.
- `Clear` - clears all the class properties and database columns mappings.
- `Get` - gets the mapped name of the class property.
- `Remove` - removes the exising mapping between the class property and database column.

### Use-Cases

You should use this class if you do not like to use the [Map](/attribute/map) attribute. Usually, the purpose of the usability is to make sure that the model is attribute-free and is not bound to a specific ORM.

### How to use?

To add a mapping, simply call the `Add()` method.

```csharp
PropertyMapper.Add<Customer>(c => c.FirstName, "[FName]", true);
PropertyMapper.Add<Customer>(c => c.LastName, "[LName]", true);
PropertyMapper.Add<Customer>(c => c.DateOfBirth, "[DOB]", true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get()` method.

```csharp
var mappedName = PropertyMapper.Get<Customer>(c => c.FirstName);
```

> Please consider to always use the [PropertyMappedNameCache](/cacher/classmappednamecache) class when extracting the mapped property name.

To remove the mapping, use the `Remove()` method.

```csharp
PropertyMapper.Remove<Customer>(c => c.FirstName);
```