---
layout: default
title: CompareInfo
permalink: /attribute/sqlserver/compareinfo
tags: [repodb, attribute, compareinfo, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# CompareInfo

---

This attribute is used to set the value of the `SqlParameter.CompareInfo` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [CompareInfo(SqlCompareOptions.IgnoreCase)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new CompareInfoAttribute(SqlCompareOptions.IgnoreCase) })
```