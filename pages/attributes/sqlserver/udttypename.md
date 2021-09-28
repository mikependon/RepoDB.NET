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

Below a sample code on how to use this attribute.

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
    .PropertyValueAttributes(e => e.Name, new UdtTypeNameAttribute("UdtTypeName"));
```

### Retrieval

You can retrieve the list of attributes via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name, includeMappings: true)?
    .FirstOrDefault(e => e.GetType() == typeof(UdtTypeNameAttribute));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(UdtTypeNameAttribute));
```

> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.