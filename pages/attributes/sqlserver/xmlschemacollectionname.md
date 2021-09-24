---
layout: default
title: XmlSchemaCollectionName
permalink: /attribute/sqlserver/xmlschemacollectionname
tags: [repodb, attribute, xmlschemacollectionname, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# XmlSchemaCollectionName

---

This attribute is used to set the value of the `SqlParameter.XmlSchemaCollectionName` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [XmlSchemaCollectionName("XmlSchemaCollectionName")]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new XmlSchemaCollectionName("XmlSchemaCollectionName") })
```