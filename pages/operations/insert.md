---
layout: page
title: "Insert (RepoDb)"
permalink: /operation/insert
tags: [repodb, tutorial, insert, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Insert

This method is used to insert a record in the database.

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

#### Learnings

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Below is a sample code to insert a row into the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var id = connection.Insert<Person>(new Person
	{
		Name = "John Doe",
		Address = "New York",
		DateOfBirth = DateTime.Parse("2020-01-01"),
		IsActive = true,
		DateInsertedUtc = DateTime.UtcNow
	});
}
```

> The result is always the value of primary key. If the primary key is identity, then the newly generated identity value will be returned.

#### Targetting a Table

You can also target a specific table by passing the literal table and dynamic object like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = new
	{
		Name = "John Doe",
		Address = "New York",
		DateOfBirth = DateTime.Parse("2020-01-01"),
		IsActive = true,
		DateInsertedUtc = DateTime.UtcNow
	};
	var id = connection.Insert("[dbo].[Person]",
		entity: person);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var id = connection.Insert<Person>(person,
		hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var id = connection.Insert<Person>(person,
		hints: SqlServerTableHints.TabLock);
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
			var id = connection.Insert<Person>(person,
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


