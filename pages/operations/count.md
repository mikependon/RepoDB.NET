---
layout: navpage
sidebar: operations
title: "Count (RepoDb)"
permalink: /operation/count
tags: [repodb, tutorial, count, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Count

This method is used to count the number rows on a single table from the database.

#### Data Providers

Below are the supported data providers by this operation.

- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

#### Installation

To install, simply type the codes below in your Package Manager Console.

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

Below is a sample code that counts the rows from the `[dbo].[Person]` table since yesterday.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var counted = connection.Count<Person>(e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1));
}
```

#### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var counted = connection.Count("[dbo].[Person]", new { State = "Michigan" });
}
```

Or, use the [QueryGroup](/class/querygroup) or [QueryField](/class/queryfield) if you are to enhance the *where* expressions.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var where = new []
	{
		new QueryField("DateInsertedUtc", Operation.GreaterThanOrEqual, DateTime.UtcNow.Date.AddDays(-1))
	}
	var counted = connection.Count("[dbo].[Person]", where: where);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var counted = connection.Count<Person>(e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
		hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var counted = connection.Count<Person>(e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
		hints: SqlServerTableHints.NoLock);
}
```
