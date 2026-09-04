---
layout: default
title: EnterpriseDbType
permalink: /attribute/enterprisedb/enterprisedbtype
tags: [repodb, attribute, enterprisedbtype]
parent: "EnterpriseDB"
grand_parent: ATTRIBUTES
---

# EnterpriseDbType

---

This attribute sets the `EDBParameter.EDBDbType` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [EnterpriseDbType(EDBDbType.Varchar)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new EnterpriseDbTypeAttribute(EDBDbType.Varchar));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(EnterpriseDbTypeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
