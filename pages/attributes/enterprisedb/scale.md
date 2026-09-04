---
layout: default
title: Scale
permalink: /attribute/enterprisedb/scale
tags: [repodb, attribute, scale]
parent: "EnterpriseDB"
grand_parent: ATTRIBUTES
---

# Scale

---

This attribute sets the `EDBParameter.Scale` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [Scale(2)]
    public decimal Salary { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Salary, new ScaleAttribute(2));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Salary)?
    .FirstOrDefault(e => e.GetType() == typeof(ScaleAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
