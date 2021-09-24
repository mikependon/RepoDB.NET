---
layout: default
title: DbType
permalink: /attribute/parameter/dbtype
tags: [repodb, attribute, dbtype, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Parameter
grand_parent: ATTRIBUTES
---

# DbType

---

This attribute is used to set the value of the `DbParameter.DbType` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [DbType(DbType.NVarChar)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new DbTypeAttribute(DbType.NVarChar) })
```