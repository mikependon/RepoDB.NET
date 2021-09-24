---
layout: default
title: DataTypeName
permalink: /attribute/npgsql/datatypename
tags: [repodb, attribute, datatypename, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: PostgreSQL
grand_parent: ATTRIBUTES
---

# DataTypeName

---

This attribute is used to set the value of the `NpgsqlParameter.DataTypeName` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [DataTypeName("DataTypeName")]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new DataTypeNameAttribute("DataTypeName") })
```