---
layout: default
title: SourceVersion
permalink: /attribute/db2/sourceversion
tags: [repodb, attribute, sourceversion]
parent: "Db2"
grand_parent: ATTRIBUTES
---

# SourceVersion

---

This attribute sets the `DB2Parameter.SourceVersion` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [SourceVersion(DataRowVersion.Current)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new SourceVersionAttribute(DataRowVersion.Current));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SourceVersionAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
