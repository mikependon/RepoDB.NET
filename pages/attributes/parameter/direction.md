---
layout: default
title: Direction
permalink: /attribute/parameter/direction
tags: [repodb, attribute, direction]
parent: Parameter
grand_parent: ATTRIBUTES
---

# Direction

---

This attribute sets the `DbParameter.Direction` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    public string Name { get; set; }

    [Direction(ParameterDirection.InputOutput)]
    public double Assets { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Assets, new ParameterDirectionAttribute(ParameterDirection.InputOutput));
```

{: .note }
> Properties with an output direction (`Output`, `InputOutput`, `ReturnValue`) are not yet updated with values returned from the database. This capability will be added in a future release.

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ParameterDirectionAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ParameterDirectionAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
