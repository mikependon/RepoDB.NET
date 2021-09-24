---
layout: default
title: XmlSchemaCollectionDatabase
permalink: /attribute/sqlserver/xmlschemacollectiondatabase
tags: [repodb, attribute, xmlschemacollectiondatabase, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
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
    .PropertyValueAttributes(e => e.Name, new [] { new UdtTypeNameAttribute("XmlSchemaCollectionDatabase") })
```