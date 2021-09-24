---
layout: default
title: ConvertedValue
permalink: /attribute/npgsql/convertedvalue
tags: [repodb, attribute, convertedvalue, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: PostgreSQL
grand_parent: ATTRIBUTES
---

# ConvertedValue

---

This attribute is used to set the value of the `NpgsqlParameter.ConvertedValue` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [ConvertedValue("ConvertedValue")]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new ConvertedValueAttribute("ConvertedValue") })
```