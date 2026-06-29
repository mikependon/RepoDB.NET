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

This attribute sets the `NpgsqlParameter.NpgsqlDbType` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [NpgsqlDbType(NpgsqlDbType.Text)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new NpgsqlDbTypeAttribute(NpgsqlDbType.Text));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(NpgsqlDbTypeAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(NpgsqlDbTypeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
