---
layout: default
sidebar: attributes
title: "TypeMap"
nav_order: 6
description: "Learn on how to use the RepoDB TypeMap attribute."
permalink: /attribute/typemap
tags: [repodb, typemap]
parent: ATTRIBUTES
---

# TypeMap

---

This attribute maps a property to a specific database type via `System.Data.DbType`.

{: .important }
> This attribute takes precedence over the type-level mapping defined in [TypeMapper](/mapper/typemapper).

The following example maps a [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0) property to `DbType.DateTime2`:

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    [TypeMap(DbType.DateTime2)] // Mapping this to 'DateTime2'
    public DateTime DateOfBirth { get; set; }
}
```

The following example maps a `byte[]` property to `DbType.Binary`:

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    [TypeMap(DbType.Binary)] // Mapping this to 'Binary'
    public byte[] Image { get; set; }
}
```