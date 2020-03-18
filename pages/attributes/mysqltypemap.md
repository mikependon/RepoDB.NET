---
layout: page
title: "MySqlTypeMapAttribute (RepoDb)"
permalink: /attribute/mysqltypemap
tags: [repodb, class, mysqltypemap, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## MySqlTypeMapAttribute

This attribute is used to map a property into its equivalent type from the database (via `System.Data.SqlDbType`).

It only supports the [MySQL](https://www.nuget.org/packages/RepoDb.MySql).

#### How to Use?

Below is a a sample code on how to map an existing property into a `MySql.Data.MySqlClient.MySqlDbType` database type.

```csharp
public class Person
{
	public int Id { get; set; }
	[MySqlTypeMapAttribute(MySqlDbType.VarChar)] // Mapping this to a 'VarChar'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public int Age { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```

Or the mapping the `byte[]` to be a `byte`.

```csharp
public class Person
{
	public int Id { get; set; }
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public int Age { get; set; }
	[MySqlTypeMapAttribute(MySqlDbType.Byte)] // Mapping this to a 'Byte'
	public byte[] Image { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```