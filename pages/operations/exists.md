---
layout: page
title: "Exists (RepoDb)"
permalink: /operation/exists
tags: [repodb, tutorial, exists, orm, hybrid-orm, sqlserver]
---

## Exists

This method is used to check whether a record is present in the database.

##### Supported Data Providers

Below are the supported data providers by this operation.

- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

### Learnings

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Below is a sample code to check whether a row is existing from the *[dbo].[Person]* table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var existing = connection.Exists<Person>(10045);
}
```

Or, you can use the *Linq* to enhance the expression.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var existing = connection.Exists<Person>(e => e.Name == "Doe" && e.DateOfBirth =  DateTime.Parse("2000-01-01"));
}
```

##### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var existing = connection.Exists("[dbo].[Person]", 10045);
}
```

Or, you can use the *Query Object* to enhance the expression.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var where = new []
	{
		new QueryField("Name", "Doe"),
		new QueryField("DateOfBirth", DateTime.Parse("2000-01-01"))
	};
	var existing = connection.Exists("[dbo].[Person]", where);
}
```

### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var existing = connection.Exists<Person>(10045,
		hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var existing = connection.Exists<Person>(10045,
		hints: SqlServerTableHints.NoLock);
}
```

### Passing a Transaction

To pass a transaction on this method, simply create an instance of `IDbConnection` and pass it at the `transaction` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var transaction = connection.BeginTransaction())
	{
		try
		{
			var existing = connection.Exists<Person>(10045,
				transaction: transaction);

			transaction.Commit();
		}
		catch (Exception ex)
		{
			// Handles the 'ex' here
		}
	}
}
```

> To commit the transaction, you have to explicitly call the `Commit()` method.

