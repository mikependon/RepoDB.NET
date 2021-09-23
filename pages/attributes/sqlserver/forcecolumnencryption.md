---
layout: default
title: ForceColumnEncryption
permalink: /attribute/sqlserver/forcecolumnencryption
tags: [repodb, forcecolumnencryption, dbtype, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: SQLSRVR
grand_parent: ATTRIBUTES
---

# ForceColumnEncryption

---

This attribute is used to set the value of the `SqlParameter.ForceColumnEncryption` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

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
    .PropertyValueAttributes(e => e.Name, new [] { new ForceColumnEncryptionAttribute(true) })
```