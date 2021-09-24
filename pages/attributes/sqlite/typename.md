---
layout: default
title: TypeName
permalink: /attribute/sqlite/typename
tags: [repodb, attribute, typename, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: SQLite
grand_parent: ATTRIBUTES
---

# TypeName

---

This attribute is used to set the value of the `SQLiteParameter.TypeName` property via a class property.

> Only works on `System.Data.SQLite` package.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [TypeName("TypeName")]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new TypeNameAttribute("TypeName") })
```