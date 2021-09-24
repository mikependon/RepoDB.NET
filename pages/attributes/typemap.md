---
layout: default
sidebar: attributes
title: "TypeMap"
nav_order: 6
description: "Learn on how to use the RepoDB TypeMap attribute."
permalink: /attribute/typemap
tags: [repodb, class, typemap, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: ATTRIBUTES
---

# TypeMap

---

This attribute is used to map a property into its equivalent type from the database (via `System.Data.DbType`).

> This attribute supercede the type-level mapping defined at the [TypeMapper](/mapper/typemapper) object.

### Usability

Below is a a sample code on how to map an existing `DateTime` property to a `System.Data.DbType.DateTime2` database type.

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    [TypeMap(DbType.DateTime2)] // Mapping this to 'DateTime2'
    public DateTime DateOfBirth { get; set; }
}
```

In some realistic scenario, we tend to force some `byte[]` type to become a binary.

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    [TypeMap(DbType.Binary)] // Mapping this to 'Binary'
    public byte[] Image { get; set; }
}
```