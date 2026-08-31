---
layout: default
title: SapHanaDbType
permalink: /attribute/saphana/saphanadbtype
tags: [repodb, attribute, saphanadbtype]
parent: "SAP HANA"
grand_parent: ATTRIBUTES
---

# SapHanaDbType

---

This attribute sets the `HanaParameter.HanaDbType` property value via a class property.

{: .note }
> This is currently the only parameter attribute for SAP HANA — there is no `SourceColumn`, `SourceColumnNullMapping`, or `SourceVersion` attribute equivalent to the other providers.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [SapHanaDbType(HanaDbType.NVarChar)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new SapHanaDbTypeAttribute(HanaDbType.NVarChar));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SapHanaDbTypeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
