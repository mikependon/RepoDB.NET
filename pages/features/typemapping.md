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

### Mapping a .NET CLR type

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

### Mapping a Specific Property

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

In SQL Server, you can also map to a specific `System.Data.SqlDbType` using the [MicrosoftSqlServerTypeMap](/attribute/microsoftsqlservertypemap) (for `Microsoft.Data.SqlClient`).

```csharp
public class Person
{
	public int Id { get; set; }
	[MicrosoftSqlServerTypeMap(SqlDbType.NVarChar)] // Mapping this to 'NVarChar'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```

Or, use the [SystemSqlServerTypeMap](/attribute/systemsqlservertypemap) (for `System.Data.SqlClient`).

```csharp
public class Person
{
	public int Id { get; set; }
	[SystemSqlServerTypeMap(SqlDbType.NVarChar)] // Mapping this to 'NVarChar'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```

#### MySQL

In MySQL, you can also map to a specific `MySql.Data.MySqlClient.MySqlDbType` using the [MySqlTypeMap](/attribute/mysqltypemap).

```csharp
public class Person
{
	public int Id { get; set; }
	[MySqlTypeMapAttribute(MySqlDbType.VarChar)] // Mapping this to a 'VarChar'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```

#### PostgreSQL

In PostgreSQL, you can also map to a specific `NpgsqlTypes.NpgsqlDbType` using the [NpgsqlTypeMap](/attribute/npgsqltypemap).

```csharp
public class Person
{
	public int Id { get; set; }
	[NpgsqlTypeMapAttribute(NpgsqlDbType.Text)] // Mapping this to a 'Text'
	public string Name { get; set; }
	public DateTime DateOfBirth { get; set; }
	public DateTime DateInsertedUtc { get; set; }
}
```

> Both the MySQL and PostgreSQL type mapping is not yet supported in the [FluentMapper](/mapper/fluentmapper) object, therefore, you have to always explicitly decorate the mentioned attributes for every usage.