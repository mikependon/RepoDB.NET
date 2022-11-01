---
layout: default
sidebar: features
title: "Type Mapping"
description: "This is a feature that would allow you to map the .NET CLR type into its equivalent database type."
permalink: /feature/typemapping
tags: [repodb, typemapping, classmapping]
parent: FEATURES
---

# Type Mapping

---

This is a feature that would allow you to map the .NET CLR type into its equivalent database type.

Below are the important attributes per data provider.

| Attribute | RDBMS | Data Provider  | 
|:-------------|:-------------|
| [SqlDbTypeAttribute](/attribute/sqlserver/sqldbtype) | SQL Server | Microsoft.Data.SqlClient |
| [MySqlDbTypeAttribute](/attribute/mysql/mysqldbtype) | MySQL | MySql.Data, MySqlConnector |
| [NpgsqlDbTypeAttribute](/attribute/npgsql/npgsqldbtype) | PostgreSQL | Npgsql |
| [SqliteTypeAttribute](/attribute/sqlite/sqlitetype) | SQLite | Microsoft.Data.Sqlite |

## Mapping a .NET CLR type

To map a .NET CLR type, simply use the [TypeMapper](/mapper/typemapper) class.

```csharp
TypeMapper.Map(typeof(DateTime), DbType.DateTime2);
```

Or via [FluentMapper](/mapper/fluentmapper) class.

```csharp
FluentMapper
    .Type<DateTime>
    .DbType(DbType.DateTime2);
```

## Mapping a Specific Property

To map a specific class property, simply use the [TypeMap](/attribute/typemap) attribute.

```csharp
public class Person
{
	public int Id { get; set; }
	public string Name { get; set; }
	[TypeMap(DbType.DateTime2)] // Mapping this to 'DateTime2'
	public DateTime DateOfBirth { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```

#### SQL Server

In SQL Server, you can also map to a specific [System.Data.SqlDbType](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqldbtype?view=net-7.0) [SqlDbTypeAttribute](/attribute/sqlserver/sqldbtype).

```csharp
public class Person
{
	public int Id { get; set; }
	[SqlDbType(SqlDbType.NVarChar)] // Mapping this to 'NVarChar'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```

#### MySQL

In MySQL, you can also map to a specific [MySql.Data.MySqlClient.MySqlDbType](https://dev.mysql.com/doc/dev/connector-net/6.10/html/T_MySql_Data_MySqlClient_MySqlDbType.htm) using the [MySqlDbTypeAttribute](/attribute/mysql/mysqldbtype).

```csharp
public class Person
{
	public int Id { get; set; }
	[MySqlDbType(MySqlDbType.VarChar)] // Mapping this to a 'VarChar'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```

#### PostgreSQL

In PostgreSQL, you can also map to a specific [NpgsqlTypes.NpgsqlDbType](https://www.npgsql.org/doc/api/NpgsqlTypes.NpgsqlDbType.html) using the [NpgsqlDbTypeAttribute](/attribute/npgsql/npgsqldbtype).

```csharp
public class Person
{
	public int Id { get; set; }
	[NpgsqlDbType(NpgsqlDbType.Text)] // Mapping this to a 'Text'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```

#### SQLite

In PostgreSQL, you can also map to a specific [Microsoft.Data.Sqlite.SqliteType](https://learn.microsoft.com/en-us/dotnet/api/microsoft.data.sqlite.sqlitetype?view=msdata-sqlite-6.0.0) using the [SqliteTypeAttribute](/attribute/sqlite/sqlitetype).

```csharp
public class Person
{
	public int Id { get; set; }
    [SqliteType(SqliteType.Text)] // Mapping this to a 'Text'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```