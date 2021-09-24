---
layout: default
title: SqliteType
permalink: /attribute/sqlite/sqlitetype
tags: [repodb, attribute, sqlitetype, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: SQLite
grand_parent: ATTRIBUTES
---

# SqliteType

---

This attribute is used to set the value of the `MySqlParameter.SqliteType` property via a class property.

> Only works on `Microsoft.Data.Sqlite` package.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [SqliteType(SqliteType.Text)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new SqliteTypeAttribute(SqliteType.Text) })
```