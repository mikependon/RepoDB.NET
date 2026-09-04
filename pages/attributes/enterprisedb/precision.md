---
layout: default
title: Precision
permalink: /attribute/enterprisedb/precision
tags: [repodb, attribute, precision]
parent: "EnterpriseDB"
grand_parent: ATTRIBUTES
---

# Precision

---

This attribute sets the `EDBParameter.Precision` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [Precision(10)]
    public decimal Salary { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Salary, new PrecisionAttribute(10));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Salary)?
    .FirstOrDefault(e => e.GetType() == typeof(PrecisionAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
