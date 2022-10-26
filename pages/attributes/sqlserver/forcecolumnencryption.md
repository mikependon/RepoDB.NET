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

This attribute is used to set the value of the `SqlParameter.ForceColumnEncryption` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [ForceColumnEncryption(true)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new ForceColumnEncryptionAttribute(true));
```

### Retrieval

You can retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name, includeMappings: true)?
    .FirstOrDefault(e => e.GetType() == typeof(ForceColumnEncryptionAttribute));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ForceColumnEncryptionAttribute));
```

> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.