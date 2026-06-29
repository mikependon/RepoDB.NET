---
layout: default
title: ForceColumnEncryption
permalink: /attribute/sqlserver/forcecolumnencryption
tags: [repodb, attribute, forcecolumnencryption]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# ForceColumnEncryption

---

This attribute sets the `SqlParameter.ForceColumnEncryption` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [ForceColumnEncryption(true)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new ForceColumnEncryptionAttribute(true));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ForceColumnEncryptionAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ForceColumnEncryptionAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
