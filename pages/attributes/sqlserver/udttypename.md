---
layout: default
title: UdtTypeName
permalink: /attribute/sqlserver/udttypename
tags: [repodb, attribute, udttypename, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# UdtTypeName

---

This attribute is used to set the value of the `SqlParameter.TypeNUdtTypeNameame` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [UdtTypeName("UdtTypeName")]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new UdtTypeNameAttribute("UdtTypeName") })
```