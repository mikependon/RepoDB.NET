---
layout: default
title: Offset
permalink: /attribute/sqlserver/offset
tags: [repodb, attribute, offset, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# Offset

---

This attribute is used to set the value of the `SqlParameter.Offset` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [Offset(1)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new OffsetAttribute(1));
```

### Retrieval

You can retrieve the list of attributes via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name, includeMappings: true)?
    .FirstOrDefault(e => e.GetType() == typeof(OffsetAttribute));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(OffsetAttribute));
```

> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.