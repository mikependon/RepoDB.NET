---
layout: default
title: Offset
permalink: /attribute/oracle/offset
tags: [repodb, attribute, offset]
parent: "Oracle"
grand_parent: ATTRIBUTES
---

# Offset

---

This attribute sets the `OracleParameter.Offset` property value via a class property. Specifies the offset into the `Value` property (or into each element of the `Value` property, for array binds).

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [Offset(1)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new OffsetAttribute(1));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(OffsetAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
