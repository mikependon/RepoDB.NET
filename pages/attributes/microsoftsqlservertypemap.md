---
layout: navpage
sidebar: attributes
title: "MicrosoftSqlServerTypeMap"
description: "Learn on how to use the RepoDB MicrosoftSqlServerTypeMap attribute."
permalink: /attribute/microsoftsqlservertypemap
tags: [repodb, class, microsoftsqlservertypemap, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# MicrosoftSqlServerTypeMap

This attribute is used to map a property into its equivalent type from the database (via `System.Data.SqlDbType`).

It only supports the SQL Server and if you are using the `Microsoft.Data.SqlClient` namespace.

> This attribute is setting the value of the `SqlParameter.SqlDbType` property before the actual execution.

#### How to Use?

Below is a a sample code on how to map an existing property into a `System.Data.SqlDbType` database type.

```csharp
public class Person
{
	public int Id { get; set; }
	[MicrosoftSqlServerTypeMap(SqlDbType.NVarChar)] // Mapping this to 'NVarChar'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public int Age { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```