---
layout: default
title: NpgsqlDbType
permalink: /attribute/npgsql/npgsqldbtype
tags: [repodb, attribute, npgsqldbtype]
parent: PostgreSQL
grand_parent: ATTRIBUTES
---

# NpgsqlDbType

---

This attribute is used to set the value of the `NpgsqlParameter.NpgsqlDbType` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [NpgsqlDbType(NpgsqlDbType.Text)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new NpgsqlDbTypeAttribute(NpgsqlDbType.Text));
```

### Retrieval

You can retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(NpgsqlDbTypeAttribute));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(NpgsqlDbTypeAttribute));
```

{: .important }
> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.