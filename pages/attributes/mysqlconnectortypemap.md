---
layout: navpage
sidebar: attributes
title: "MySqlConnectorTypeMap"
description: "Learn on how to use the RepoDB MySqlConnectorTypeMap attribute."
permalink: /attribute/mysqltypemap
tags: [repodb, class, mysqlconnectortypemap, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# MySqlConnectorTypeMap

This attribute is used to map a property into its equivalent type from the database (via `MySql.Data.MySqlClient.MySqlDbType`).

It only supports the [MySQL](https://www.nuget.org/packages/RepoDb.MySqlConnector).

#### How to Use?

Below is a a sample code on how to map an existing property into a `MySql.Data.MySqlClient.MySqlDbType` database type.

```csharp
public class Person
{
	public int Id { get; set; }
	[MySqlConnectorTypeMap(MySqlDbType.VarChar)] // Mapping this to a 'VarChar'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public int Age { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```

Or by mapping the `byte[]` to be a `byte`.

```csharp
public class Person
{
	public int Id { get; set; }
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public int Age { get; set; }
	[MySqlConnectorTypeMap(MySqlDbType.Byte)] // Mapping this to a 'Byte'
	public byte[] Image { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```