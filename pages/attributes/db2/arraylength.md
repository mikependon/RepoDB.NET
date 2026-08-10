---
layout: default
title: ArrayLength
permalink: /attribute/db2/arraylength
tags: [repodb, attribute, arraylength]
parent: "Db2"
grand_parent: ATTRIBUTES
---

# ArrayLength

---

This attribute sets the `DB2Parameter.ArrayLength` property value via a class property. Specifies the number of elements of the `Value` property to use for parameter bind-in.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [ArrayLength(3)]
    public string[] Names { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Names, new ArrayLengthAttribute(3));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Names)?
    .FirstOrDefault(e => e.GetType() == typeof(ArrayLengthAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
