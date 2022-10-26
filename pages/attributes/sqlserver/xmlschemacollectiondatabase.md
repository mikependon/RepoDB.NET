---
layout: default
title: XmlSchemaCollectionDatabase
permalink: /attribute/sqlserver/xmlschemacollectiondatabase
tags: [repodb, attribute, xmlschemacollectiondatabase]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# XmlSchemaCollectionDatabase

---

This attribute is used to set the value of the `SqlParameter.XmlSchemaCollectionDatabase` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [XmlSchemaCollectionDatabase("XmlSchemaCollectionDatabase")]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new XmlSchemaCollectionDatabase("XmlSchemaCollectionDatabase"));
```

### Retrieval

You can retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name, includeMappings: true)?
    .FirstOrDefault(e => e.GetType() == typeof(XmlSchemaCollectionDatabase));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(XmlSchemaCollectionDatabase));
```

> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.