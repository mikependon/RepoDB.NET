---
layout: default
title: FbDbType
permalink: /attribute/firebird/fbdbtype
tags: [repodb, attribute, fbdbtype]
parent: "Firebird"
grand_parent: ATTRIBUTES
---

# FbDbType

---

This attribute sets the `FbParameter.FbDbType` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [FbDbType(FbDbType.VarChar)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new FbDbTypeAttribute(FbDbType.VarChar));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(FbDbTypeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
