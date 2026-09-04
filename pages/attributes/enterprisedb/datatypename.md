---
layout: default
title: DataTypeName
permalink: /attribute/enterprisedb/datatypename
tags: [repodb, attribute, datatypename]
parent: "EnterpriseDB"
grand_parent: ATTRIBUTES
---

# DataTypeName

---

This attribute sets the `EDBParameter.DataTypeName` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [DataTypeName("varchar")]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new DataTypeNameAttribute("varchar"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(DataTypeNameAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
