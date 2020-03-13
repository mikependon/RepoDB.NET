---
layout: page
title: "AverageAll (RepoDb)"
permalink: /operation/averageall
tags: [repodb, tutorial, averageall, orm, hybrid-orm, sqlserver]
---

## AverageAll

This method is used to average all records target column from the database.

##### Supported Data Providers
- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Below is a sample code that averages all the records' column *Value* from a *[dbo].[Sales]* table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var expenses = connection.AverageAll<Sales>(e => e.Value);
}
```

##### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var expenses = connection.AverageAll("[dbo].[Sales]", Field.From("Value"));
}
```

##### Hints

To pass a hints, simply write the *TableHints* and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var expenses = connection.AverageAll<Sales>(e => e.Value, hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var expenses = connection.AverageAll<Sales>(e => e.Value, hints: SqlServerTableHints.NoLock);
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
			var expenses = connection.AverageAll<Sales>(e => e.Value, transaction: transaction);

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


