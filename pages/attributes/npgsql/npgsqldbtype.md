---
layout: default
title: NpgsqlDbType
permalink: /attribute/npgsql/npgsqldbtype
tags: [repodb, attribute, npgsqldbtype, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: PostgreSQL
grand_parent: ATTRIBUTES
---

# NpgsqlDbType

---

This attribute is used to set the value of the `NpgsqlParameter.NpgsqlDbType` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [NpgsqlDbType(NpgsqlDbType.Text)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new NpgsqlDbTypeAttribute(NpgsqlDbType.Text) })
```