---
layout: default
title: IsDefault
permalink: /attribute/db2/isdefault
tags: [repodb, attribute, isdefault]
parent: "Db2"
grand_parent: ATTRIBUTES
---

# IsDefault

---

This attribute sets the `DB2Parameter.IsDefault` property value via a class property. Indicates whether the parameter uses the default value assigned by the data server.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [IsDefault(true)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new IsDefaultAttribute(true));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(IsDefaultAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
