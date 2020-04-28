---
layout: navpage
sidebar: operations
title: "Exists"
permalink: /operation/exists
tags: [repodb, tutorial, exists, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Exists

This method is used to check whether a record is present in the database.

#### Data Providers

Below are the supported data providers by this operation.

- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

#### Installation

To install, simply type the code snippets below in your Package Manager Console.

```csharp
> Install-Package RepoDb.SqlServer
```

Then call the bootstrapper once.

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

Or visit our [installation](/tutorial/installation) page for more information.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Learnings

Below is a sample code to check whether a row is existing from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var existing = connection.Exists<Person>(10045);
}
```

Or, you can use the `Linq` to enhance the expression.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var existing = connection.Exists<Person>(e => e.Name == "Doe" && e.DateOfBirth =  DateTime.Parse("2000-01-01"));
}
```

#### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var existing = connection.Exists("[dbo].[Person]", 10045);
}
```

Or, you can use the [QueryGroup](/class/querygroup) or [QueryField](/class/queryfield) to enhance the expression.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var where = new []
	{
		new QueryField("Name", "Doe"),
		new QueryField("DateOfBirth", DateTime.Parse("2000-01-01"))
	};
	var existing = connection.Exists("[dbo].[Person]", where);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var existing = connection.Exists<Person>(10045,
		hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var existing = connection.Exists<Person>(10045,
		hints: SqlServerTableHints.NoLock);
}
```
