---
layout: default
title: IsUnassigned
permalink: /attribute/db2/isunassigned
tags: [repodb, attribute, isunassigned]
parent: "Db2"
grand_parent: ATTRIBUTES
---

# IsUnassigned

---

This attribute sets the `DB2Parameter.IsUnassigned` property value via a class property. Indicates whether the parameter is treated as an unassigned value by the data server.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [IsUnassigned(true)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new IsUnassignedAttribute(true));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(IsUnassignedAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
