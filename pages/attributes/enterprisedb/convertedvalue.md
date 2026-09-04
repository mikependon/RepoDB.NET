---
layout: default
title: ConvertedValue
permalink: /attribute/enterprisedb/convertedvalue
tags: [repodb, attribute, convertedvalue]
parent: "EnterpriseDB"
grand_parent: ATTRIBUTES
---

# ConvertedValue

---

{: .warning }
> This attribute is `[Obsolete]` — `EDBParameter.ConvertedValue` is obsoleted by the `EnterpriseDB.EDBClient` driver itself. Avoid using it in new code.

This attribute sets the `EDBParameter.ConvertedValue` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [ConvertedValue("ConvertedValue")]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new ConvertedValueAttribute("ConvertedValue"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ConvertedValueAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
