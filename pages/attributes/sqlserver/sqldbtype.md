---
layout: default
title: SqlDbType
permalink: /attribute/sqlserver/sqldbtype
tags: [repodb, attribute, sqldbtype]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# SqlDbType

---

This attribute is used to set the value of the `SqlParameter.SqlDbType` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [SqlDbType(SqlDbType.NVarChar)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new SqlDbTypeAttribute(SqlDbType.NVarChar));
```

### Retrieval

You can retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name, includeMappings: true)?
    .FirstOrDefault(e => e.GetType() == typeof(SqlDbTypeAttribute));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SqlDbTypeAttribute));
```

> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.