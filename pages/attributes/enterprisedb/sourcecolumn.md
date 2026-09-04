---
layout: default
title: SourceColumn
permalink: /attribute/enterprisedb/sourcecolumn
tags: [repodb, attribute, sourcecolumn]
parent: "EnterpriseDB"
grand_parent: ATTRIBUTES
---

# SourceColumn

---

This attribute sets the `EDBParameter.SourceColumn` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [SourceColumn("Name")]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new SourceColumnAttribute("Name"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SourceColumnAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
