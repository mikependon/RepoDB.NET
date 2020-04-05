---
layout: navpage
sidebar: features
title: "Type Mapping (RepoDb)"
permalink: /feature/typemapping
tags: [repodb, class, typemapping, classmapping, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Type Mapping

This is the feature that would allow you to map the .NET CLR Type into its equivalent database type.

#### Mapping a .NET CLR Type

To map a .NET CLR type, simply use the [TypeMapper](/mapper/typemapper) class.

```csharp
TypeMapper.Map(typeof(DateTime), DbType.DateTime2);
```

#### Mapping a Specific Property

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

###### SQL Server

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

###### MySQL

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

###### PostgreSQL

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

#### Automatic Type Conversion

The class [TypeMapper](/class/typemapper) consists of property `ConversionType` which allows you to enable the automatic type conversion.

```csharp
TypeMapper.ConversionType = ConversionType.Automatic;
```

Please visit the [ConversionType](/enumeration/conversiontype) enumeration for more details.