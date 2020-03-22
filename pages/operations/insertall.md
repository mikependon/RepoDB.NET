---
layout: page
title: "InsertAll (RepoDb)"
permalink: /operation/insertall
tags: [repodb, tutorial, insertall, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## InsertAll

This method is used to insert an array of rows in the database.

#### Data Providers

Below are the supported data providers by this operation.

- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

#### Use Case

If you are inserting multiple rows in the database, do not ever "iterate and insert it in atomic way". This method solves that problem by creating a multi-packed SQL statements and pass it all in one-go.

The performance of this not comparable to the atomic way of insertion. It is more performant and efficient.

You can adjust the size of the batch on how much rows you want to process per batch. This scenario applies depends on your situation (ie: *No of Columns*, *Network Latency*, etc).

The execution is ACID as the transaction object will be created if not given.

> Be aware that if you are managing the size of your batch, it may collide on the number of maximum allowable parameters of ADO.NET. The max parameters are `2100`.

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

> In this tutorial, we will use the [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer) as the database and [C#](https://docs.microsoft.com/en-us/dotnet/csharp/) as the programming language.

Let us you have a method that returns a list of *Person* models.

```csharp
private IEnumerable<Person> GetPeople()
{
	var people = new List<Person>();
	people.Add(new Person
	{
		Name = "John Doe",
		Address = "New York",
		DateOfBirth = DateTime.Parse("2020-01-01"),
		IsActive = true,
		DateInsertedUtc = DateTime.UtcNow
	});
	people.Add(new Person
	{
		...
	});
	people.Add(new Person
	{
		...
	});
	return people;
}
```

Below is a sample code to insert a list of *Person* into the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = GetPeople();
	var insertedRows = connection.InsertAll(people);
}
```

#### Targetting a Table

You can also target a specific table by passing the literal table and dynamic object like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = GetPeople();
	var insertedRows = connection.InsertAll("[dbo].[Person]",
		entities: people);
}
```

##### Inserting Specific Columns

You can also target a specific columns to be inserted by passing the list of fields to be included in the `fields` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = GetPeople();
	var insertedRows = connection.InsertAll("[dbo].[Person]",
		entities: people,
		fields: Field.From("Id", "Name", "DateInsertedUtc"));
}
```

> Even your model has so many properties, only the `Id`, `Name` and `DateInsertedUtc` will be inserted.

#### Batch Size

You can adjust the size of your batch by simply passing the value at the `batchSize` argument. By default, it is using `10` (found at `Constant.DefaultBatchOperationSize`).

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = GetPeople();
	var insertedRows = connection.InsertAll(people,
		batchSize: 30);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var insertedRows = connection.InsertAll<Person>(person,
		hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var insertedRows = connection.InsertAll<Person>(person,
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
			var insertedRows = connection.InsertAll<Person>(person,
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


