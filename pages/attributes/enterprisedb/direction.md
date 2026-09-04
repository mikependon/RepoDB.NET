---
layout: default
title: Direction
permalink: /attribute/enterprisedb/direction
tags: [repodb, attribute, direction]
parent: "EnterpriseDB"
grand_parent: ATTRIBUTES
---

# Direction

---

This attribute sets the `EDBParameter.Direction` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [Direction(ParameterDirection.Input)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new DirectionAttribute(ParameterDirection.Input));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(DirectionAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
