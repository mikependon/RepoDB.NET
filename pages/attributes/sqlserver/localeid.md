---
layout: default
title: LocaleId
permalink: /attribute/sqlserver/localeid
tags: [repodb, attribute, localeid, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# LocaleId

---

This attribute is used to set the value of the `SqlParameter.LocaleId` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [LocaleId(100)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new LocaleIdAttribute(100) })
```