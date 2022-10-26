---
layout: default
title: CompareInfo
permalink: /attribute/sqlserver/compareinfo
tags: [repodb, attribute, compareinfo]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# CompareInfo

---

This attribute is used to set the value of the `SqlParameter.CompareInfo` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [CompareInfo(SqlCompareOptions.IgnoreCase)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new CompareInfoAttribute(SqlCompareOptions.IgnoreCase));
```

### Retrieval

You can retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name, includeMappings: true)?
    .FirstOrDefault(e => e.GetType() == typeof(CompareInfoAttribute));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(CompareInfoAttribute));
```

> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.