---
layout: default
title: ConvertedValue
permalink: /attribute/npgsql/convertedvalue
tags: [repodb, attribute, convertedvalue, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: PostgreSQL
grand_parent: ATTRIBUTES
---

# ConvertedValue

---

This attribute is used to set the value of the `NpgsqlParameter.ConvertedValue` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [ConvertedValue("ConvertedValue")]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new ConvertedValueAttribute("ConvertedValue"));
```

### Retrieval

You can retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name, includeMappings: true)?
    .FirstOrDefault(e => e.GetType() == typeof(ConvertedValueAttribute));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ConvertedValueAttribute));
```

> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.