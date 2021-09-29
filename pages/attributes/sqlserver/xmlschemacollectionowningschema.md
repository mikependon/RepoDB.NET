---
layout: default
title: XmlSchemaCollectionOwningSchema
permalink: /attribute/sqlserver/xmlschemacollectionowningschema
tags: [repodb, attribute, xmlschemacollectionowningschema, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# XmlSchemaCollectionOwningSchema

---

This attribute is used to set the value of the `SqlParameter.XmlSchemaCollectionOwningSchema` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [XmlSchemaCollectionName("XmlSchemaCollectionOwningSchema")]
    public string XmlSchemaCollectionOwningSchema { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new XmlSchemaCollectionOwningSchema("XmlSchemaCollectionOwningSchema"));
```

### Retrieval

You can retrieve the list of attributes via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name, includeMappings: true)?
    .FirstOrDefault(e => e.GetType() == typeof(XmlSchemaCollectionOwningSchema));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(XmlSchemaCollectionOwningSchema));
```

> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.