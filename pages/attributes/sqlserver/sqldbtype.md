---
layout: default
title: SqlDbType
permalink: /attribute/sqlserver/sqldbtype
tags: [repodb, attribute, sqldbtype, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# SqlDbType

---

This attribute is used to set the value of the `SqlParameter.SqlDbType` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [SqlDbType(SqlDbType.NVarChar)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new SqlDbTypeAttribute(SqlDbType.NVarChar) })
```