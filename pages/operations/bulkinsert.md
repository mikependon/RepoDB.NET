---
layout: page
title: "BulkInsert (RepoDb)"
permalink: /operation/bulkinsert
tags: [repodb, tutorial, bulkinsert, orm, hybrid-orm, sqlserver]
---

## BulkInsert

This method is used to bulk-insert all rows from the client into the database.

For now, this operation only supports [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations).

#### Use Case

This method is very useful if you are inserting multiple rows towards the database. It is high-performant in nature as it is using the real bulk operation natively from ADO.NET (via `SqlBulkCopy` class).

If you are working to insert range of rows from 1000 or beyond, then use this method over [InsertAll](/operation/insertall).

#### Installation

To install, simply type the codes below in your Package Manager Console.

```csharp
> Install-Package RepoDb.SqlServer.BulkOperations
```

Then call the bootstrapper once.

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

#### Learnings

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

Let us say you have a methods that create a list of *Person* from the client application.

```csharp
private IEnumerable<Person> GetPeople(int count = 1000)
{
	for (var i = 0; i < count; i++)
	{
		yield return new Person
		{
			Name = $"Person-{i}",
			SSN = Guid.NewGuid().ToString(),
			IsActive = true,
			DateInsertedUtc = DateTime.UtcNow
		};
	}
}
```

Then, below is the code that bulk-inserts 10K of rows towards `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = GetPeople(10000);
	var insertedRows = connection.BulkInsert(people);
}
```

##### DataTable

Below is a sample code to bulk-insert by data table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = GetPeople(10000);
	var table = ConvertToDataTable(people);
	var insertedRows = connection.BulkInsert<Person>(table);
}
```

##### DataReader

Below is a sample code to bulk-insert by data reader.

```csharp
using (var sourceConnection = new SqlConnection(sourceConnectionString))
{
	using (var reader = sourceConnection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
	{
		using (var destinationConnection = new SqlConnection(destinationConnectionString))
		{
			var rows = destinationConnection.BulkInsert<Person>(reader);
		}
	}
}
```

Or you can also convert the list of *Person* to a `DataEntityDataReader` object and bulk-insert it.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = GetPeople(10000);
	using (var reader = new DataEntityDataReader<Person>(people))
	{
		var insertedRows = connection.BulkInsert<Person>(reader);
	}
}
```

#### Column Mappings

You can add a mapping via `BulkInsertMapItem` class.

```csharp
var mappings = new List<BulkInsertMapItem>();

// Add the mappings
mappings.Add(new BulkInsertMapItem("Id", "Id"));
mappings.Add(new BulkInsertMapItem("Name", "Name"));
mappings.Add(new BulkInsertMapItem("IsActive", "Active"));
mappings.Add(new BulkInsertMapItem("DateInsertedUtc", "CreatedDateUtc"));

// Execute
using (var connection = new SqlConnection(connectionString))
{
	var people = GetPeople(10000);
	var insertedRows = connection.BulkInsert(people,
		mappings: mappings);
}
```

#### Bulk Copy Options

You can define your bulk-copy options by passing a value of `SqlBulkCopyOptions` in the `options` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = GetPeople(10000);
	var insertedRows = connection.BulkInsert(people,
		options: SqlBulkCopyOptions.KeepIdentity);
}
```

#### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var people = GetPeople(10000);
	var insertedRows = connection.BulkInsert("[dbo].[Person]", people);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var people = GetPeople(10000);
	var insertedRows = connection.BulkInsert(people,
		hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = GetPeople(10000);
	var insertedRows = connection.BulkInsert(people,
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
			var people = GetPeople(10000);
			var insertedRows = connection.BulkInsert(people,
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


