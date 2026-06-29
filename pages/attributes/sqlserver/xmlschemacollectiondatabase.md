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

This attribute sets the `SqlParameter.XmlSchemaCollectionDatabase` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [XmlSchemaCollectionDatabase("XmlSchemaCollectionDatabase")]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new XmlSchemaCollectionDatabase("XmlSchemaCollectionDatabase"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(XmlSchemaCollectionDatabase));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(XmlSchemaCollectionDatabase));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
