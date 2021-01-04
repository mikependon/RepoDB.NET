---
layout: default
sidebar: attributes
title: "NpgsqlTypeMap"
description: "Learn on how to use the RepoDB NpgsqlTypeMap attribute."
permalink: /attribute/npgsqltypemap
tags: [repodb, class, npgsqltypemap, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: ATTRIBUTES
---

# NpgsqlTypeMap

---

This attribute is used to map a property into its equivalent type from the database (via `NpgsqlTypes.NpgsqlDbType`).

It only supports the [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql).

### How to use?

Below is a a sample code on how to map an existing property into a `NpgsqlTypes.NpgsqlDbType` database type.

```csharp
public class Person
{
    public int Id { get; set; }
    [NpgsqlTypeMap(NpgsqlDbType.Text)] // Mapping this to a 'Text'
    public string Name { get; set; }
    public DateTime DateOfBirth { get; set; }
    public int Age { get; set; }
    public DateTime DateInsertedUtc { get; set; }
}
```

Or by mapping the `int[]` to be an `Array`.

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime DateOfBirth { get; set; }
    public int Age { get; set; }
    [NpgsqlTypeMap(NpgsqlDbType.Array)] // Mapping this to a 'Array'
    public int[] ReferenceNumbers { get; set; }
    public DateTime DateInsertedUtc { get; set; }
}
```

> This attribute is currently not supported by the [FluentMapper](/mapper/fluentmapper).
