---
layout: default
title: Scale
permalink: /attribute/parameter/scale
tags: [repodb, attribute, scale]
parent: Parameter
grand_parent: ATTRIBUTES
---

# Scale

---

This attribute sets the `DbParameter.Scale` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    public string Name { get; set; }

    [Scale(1)]
    public double Assets { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Assets, new ScaleAttribute(1));
```

{: .note }
> The value from the database table or view takes precedence if present.

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ScaleAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ScaleAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
