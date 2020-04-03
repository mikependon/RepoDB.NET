---
layout: page
title: "QueryMultiple (RepoDb)"
permalink: /operation/querymultiple
tags: [repodb, tutorial, querymultiple, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## QueryMultiple

This method is used to fetch the multiple resultsets from the database.

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

Or visit our [installation](/tutorials/installation) page for more information.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Learnings

Below is a sample code to fetch a single parent row from the `[dbo].[Customer]` table and all the related orders made from the `[dbo].[Order]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
		o => o.PersonId == 10045);
	var customer = result.Item1.FirstOrDefault();
	var orders = result.Item2.AsList();

	// Do the stuffs for the `customer` and `orders` here
}
```

> It returns a type of `Tuple<T1, .., T7>` object. The maximum type that it can cater is 7. Each item in the tuple object is an `IEnumerable<T>` object of the target generic type.

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
		o => o.PersonId == 10045,
		hints1: "WITH (NOLOCK)",
		hints2: "WITH (NOLOCK)");
	var customer = result.Item1.FirstOrDefault();
	var orders = result.Item2.AsList();

	// Do the stuffs for the `customer` and `orders` here
}
```

Or, you can use the [SqlServerTableHints](/class[SqlServerTableHints](/class/sqlservertablehints)) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
		o => o.PersonId == 10045,
		hints1: SqlServerTableHints.TabLock,
		hints2: SqlServerTableHints.TabLock);
	var customer = result.Item1.FirstOrDefault();
	var orders = result.Item2.AsList();

	// Do the stuffs for the `customer` and `orders` here
}
```

#### Ordering the Results

To order the results, you have to pass an array of `OrderField` objects in the `orderBy` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var orderBy = OrderField.Parse(new
	{
		OrderDateUtc = RepoDb.Enumerations.Order.Descending
	});
	var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
		o => o.PersonId == 10045,
		orderBy2: orderBy);
	var customer = result.Item1.FirstOrDefault();
	var orders = result.Item2.AsList();

	// Do the stuffs for the `customer` and `orders` here
}
```

#### Filtering the Results

To filter the results, you have to pass a value at the `top` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
		o => o.PersonId == 10045,
		top2: 100);
	var customer = result.Item1.FirstOrDefault();
	var orders = result.Item2.AsList();

	// Do the stuffs for the `customer` and `orders` here
}
```

#### Passing a Transaction

To pass a transaction on this method, simply create an instance of `IDbConnection` and pass it at the `transaction` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var transaction = connection.BeginTransaction())
	{
		try
		{
			var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
				o => o.PersonId == 10045,
				transaction: transaction);
			var customer = result.Item1.FirstOrDefault();
			var orders = result.Item2.AsList();

			// Do the stuffs for the `customer` and `orders` here

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


