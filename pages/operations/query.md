---
layout: page
title: "Query (RepoDb)"
permalink: /operation/query
tags: [repodb, tutorial, query, orm, hybrid-orm, sqlserver]
---

## Query

This method is used to query a record from the database.

##### Supported Data Providers

Below are the supported data providers by this operation.

- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

### Learnings

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Below is a sample code to query a row from the *[dbo].[Person]* table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = connection.Query<Person>(10045).FirstOrDefault();
}
```

You can also query via expression.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = connection.Query<Person>(e => e.Id == 10045).FirstOrDefault();
}
```

Or like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = connection.Query<Person>(e => e.FirstName == "John" && e.LastName == "Doe").FirstOrDefault();
}
```

##### Targetting a Table

You can also target a specific table by passing the literal table like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = connection.Query("[dbo].[Person]",
		10045).FirstOrDefualt();
}
```

> The result would is a dynamic object of type `ExpandoObject`.

### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = connection.Query<Person>(10045,
		hints: "WITH (NOLOCK)").FirstOrDefault();
}
```

Or, you can use the [SqlServerTableHints](/class/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = connection.Query<Person>(10045,
		hints: SqlServerTableHints.TabLock).FirstOrDefault();
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
			var id = connection.Query<Person>(person,
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


