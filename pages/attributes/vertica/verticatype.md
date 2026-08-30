---
layout: default
title: VerticaType
permalink: /attribute/vertica/verticatype
tags: [repodb, attribute, verticatype]
parent: "Vertica"
grand_parent: ATTRIBUTES
---

# VerticaType

---

This attribute sets the `VerticaParameter.Type` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [VerticaType(VerticaType.VarChar)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new VerticaTypeAttribute(VerticaType.VarChar));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(VerticaTypeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
