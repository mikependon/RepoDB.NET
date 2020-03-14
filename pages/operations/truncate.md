---
layout: page
title: "Truncate (RepoDb)"
permalink: /operation/truncate
tags: [repodb, tutorial, count, orm, hybrid-orm, sqlserver]
---

## Truncate

This method is used to truncate all the rows on a single table from the database.

##### Supported Data Providers

Below are the supported data providers by this operation.

- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

### Leanings

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Below is a sample code that truncates the *[dbo].[Person]* table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	connection.Truncate<Person>();
}
```

##### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	connection.Truncate("[dbo].[Person]");
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
			connection.Truncate<Person>(transaction: transaction);

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


