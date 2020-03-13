---
layout: page
title: "BatchQuery (RepoDb)"
permalink: /operation/batchquery
tags: [repodb, tutorial, batchquery, orm, hybrid-orm, sqlserver]
---

## Average

This method is used to query a records from the database by batch.

##### Supported Data Providers
- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Below is a sample code that query the 1st 20 batch of active rows from the *[dbo].[Person]* table based on the date creation.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending });
	var people = connection.BatchQuery<Person>(page: 0,
		rowsPerBatch: 20,
		orderBy: orderBy,
		where: e => e.IsActive == true);
}
```

> Please be aware that the page is starting at `0`.

##### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending })
	var people = connection.BatchQuery("Person",
		page: 0,
		batchSize: 20
		orderBy: orderBy,
		where: new { IsActive = true });
}
```

Or, use the *Query Object(s)* if you are using multiple *where* columns.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var where = new []
	{
		new QueryField("IsActive", true),
		new QueryField("DateInsertedUtc", DateTime.UtcNow.Date.AddDays(-1))
	}
	var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending })
	var people = connection.BatchQuery("Person",
		page: 0,
		batchSize: 20
		orderBy: orderBy,
		where: where);
}
```

##### Hints

You can also pass a hints.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending })
	var people = connection.BatchQuery<Person>(page: 0,
		batchSize: 20
		orderBy: orderBy,
		where: e => e.IsActive == true,
		hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/classes/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending })
	var people = connection.BatchQuery<Person>(page: 0,
		batchSize: 20
		orderBy: orderBy,
		where: e => e.IsActive == true,
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
			var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending });
			var people = connection.BatchQuery<Person>(page: 0,
				rowsPerBatch: 20,
				orderBy: orderBy,
				where: e => e.IsActive == true,
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


