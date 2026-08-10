---
layout: default
title: Db2TypeOutput
permalink: /attribute/db2/db2typeoutput
tags: [repodb, attribute, db2typeoutput]
parent: "Db2"
grand_parent: ATTRIBUTES
---

# Db2TypeOutput

---

This attribute sets the `DB2Parameter.DB2TypeOutput` property value via a class property. When `true`, an output-only parameter's value is returned as a native Db2 data type (a class or struct in the `IBM.Data.Db2Types` namespace) instead of a plain .NET type.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [Db2TypeOutput(true)]
    public DateTime CreatedDateUtc { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.CreatedDateUtc, new Db2TypeOutputAttribute(true));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.CreatedDateUtc)?
    .FirstOrDefault(e => e.GetType() == typeof(Db2TypeOutputAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance. See also [Db2Type](/attribute/db2/db2type).
