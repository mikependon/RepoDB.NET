---
layout: default
title: SourceVersion
permalink: /attribute/enterprisedb/sourceversion
tags: [repodb, attribute, sourceversion]
parent: "EnterpriseDB"
grand_parent: ATTRIBUTES
---

# SourceVersion

---

This attribute sets the `EDBParameter.SourceVersion` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [SourceVersion(DataRowVersion.Default)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new SourceVersionAttribute(DataRowVersion.Default));
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
