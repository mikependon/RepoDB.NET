---
layout: default
title: Db2Type
permalink: /attribute/db2/db2type
tags: [repodb, attribute, db2type]
parent: "Db2"
grand_parent: ATTRIBUTES
---

# Db2Type

---

This attribute sets the `DB2Parameter.DB2Type` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [Db2Type(DB2Type.VarChar)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new Db2TypeAttribute(DB2Type.VarChar));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(Db2TypeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance. See also [Db2TypeOutput](/attribute/db2/db2typeoutput).
