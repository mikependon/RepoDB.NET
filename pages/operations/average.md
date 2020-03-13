---
layout: page
title: "Average (RepoDb)"
permalink: /operations/average
tags: [repodb, tutorial, average, orm, hybrid-orm, sqlserver]
---

## Average

This method is used to average a column from the database.

##### Supported Data Providers
- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Below is a sample code that averages the column *Value* from a *[dbo].[Sales]* table for a specific customer since yesterday.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customerExpenses = connection.Average<Sales>(e => e.Value,
		e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1));
}
```

##### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customerExpenses = connection.Average("[dbo].[Sales]", Field.From("Value"), new { State = "Michigan" });
}
```

Or, use the *Query Object(s)* if you are using multiple *where* columns.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var where = new []
	{
		new QueryField("CustomerId", 10045),
		new QueryField("DateInsertedUtc", DateTime.UtcNow.Date.AddDays(-1))
	}
	var customerExpenses = connection.Average("[dbo].[Sales]", Field.From("Value"), where);
}
```

##### Hints

To pass a hints, simply write the *TableHints* and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customerExpenses = connection.Average<Sales>(e => e.Value,
		e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
		hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customerExpenses = connection.Average<Sales>(e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
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
			var customerExpenses = connection.Average<Sales>(e => e.Value,
				e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
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


