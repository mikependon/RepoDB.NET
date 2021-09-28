---
layout: default
title: DbType
permalink: /attribute/parameter/dbtype
tags: [repodb, attribute, dbtype, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Parameter
grand_parent: ATTRIBUTES
---

# DbType

---

This attribute is used to set the value of the `DbParameter.DbType` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [DbType(DbType.NVarChar)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new DbTypeAttribute(DbType.NVarChar));
```

### Retrieval

You can retrieve the list of attributes via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name, includeMappings: true)?
    .FirstOrDefault(e => e.GetType() == typeof(DbTypeAttribute));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(DbTypeAttribute));
```

> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.
