---
layout: default
title: TypeName
permalink: /attribute/sqlserver/typename
tags: [repodb, typename, dbtype, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: SQLSRVR
grand_parent: ATTRIBUTES
---

# TypeName

---

This attribute is used to set the value of the `SqlParameter.TypeName` property via a class property.

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