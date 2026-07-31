---
layout: default
title: OracleDbTypeEx
permalink: /attribute/oracle/oracledbtypeex
tags: [repodb, attribute, oracledbtypeex]
parent: "Oracle"
grand_parent: ATTRIBUTES
---

# OracleDbTypeEx

---

This attribute sets the `OracleParameter.OracleDbTypeEx` property value via a class property. Unlike [OracleDbType](/attribute/oracle/oracledbtype), binding via this property causes the output value to be returned as a plain .NET type instead of an Oracle-specific wrapper type (e.g. plain `DateTime` instead of `OracleDate`).

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [OracleDbTypeEx(OracleDbType.TimeStamp)]
    public DateTime CreatedDateUtc { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.CreatedDateUtc, new OracleDbTypeExAttribute(OracleDbType.TimeStamp));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.CreatedDateUtc)?
    .FirstOrDefault(e => e.GetType() == typeof(OracleDbTypeExAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
