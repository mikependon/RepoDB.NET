---
layout: page
title: "MaxAll (RepoDb)"
permalink: /operation/maxall
tags: [repodb, tutorial, maxall, orm, hybrid-orm, sqlserver]
---

## MaxAll

This method is used to get the maximum value of the column from the database.

##### Supported Data Providers

Below are the supported data providers by this operation.

- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

### Leanings

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Below is a sample code that returns the maximum value of the column *Value* from a *[dbo].[Sales]* table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var expenses = connection.MaxAll<Sales>(e => e.Value);
}
```

##### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var expenses = connection.MaxAll("[dbo].[Sales]", Field.From("Value"));
}
```

### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var expenses = connection.MaxAll<Sales>(e => e.Value,
		hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var expenses = connection.MaxAll<Sales>(e => e.Value,
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
			var expenses = connection.MaxAll<Sales>(e => e.Value,
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


