---
layout: default
title: XmlSchemaCollectionName
permalink: /attribute/sqlserver/xmlschemacollectionname
tags: [repodb, attribute, xmlschemacollectionname]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# XmlSchemaCollectionName

---

This attribute sets the `SqlParameter.XmlSchemaCollectionName` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [XmlSchemaCollectionName("XmlSchemaCollectionName")]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new XmlSchemaCollectionName("XmlSchemaCollectionName"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(XmlSchemaCollectionName));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(XmlSchemaCollectionName));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
