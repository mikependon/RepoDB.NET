---
layout: page
title: "DeleteAll (RepoDb)"
permalink: /operation/deleteall
tags: [repodb, tutorial, count, orm, hybrid-orm, sqlserver]
---

## DeleteAll

This method is used to delete all the rows on a single table from the database.

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

#### Leanings

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Below is a sample code that delete all the rows from the *[dbo].[Person]* table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.DeleteAll<Person>();
}
```

Or you can target the list of primary keys.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var primaryKeys = new [] { 10045, 11921, 12001 }; 
	var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

#### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.DeleteAll("[dbo].[Person]");
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.DeleteAll<Person>(hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.DeleteAll<Person>(hints: SqlServerTableHints.TabLock);
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
			var deletedRows = connection.DeleteAll<Person>(transaction: transaction);

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


