---
layout: page
title: "BulkDelete (RepoDb)"
permalink: /operation/bulkdelete
tags: [repodb, tutorial, bulkdelete, orm, hybrid-orm, sqlserver]
---

## BulkDelete

This method is used to bulk-delete the target rows from the database.

##### Supported Data Providers

Below are the supported data providers by this operation.

- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)

##### Special Arguments

The arguments `qualifiers` and `usePhysicalPseudoTempTable` is provided this operation.

The argument `qualifiers` is used to define the qualifier fields to be used in the operation. It usually refers to the `WHERE` expression of SQL Statements. If not given, the primary key (or identity) field will be used.

The argument `usePhysicalPseudoTempTable` is used to define whether a physical pseudo-table will be created during the operation. By default, a temporary table (ie: `#TableName`) is used.

##### Caveats

RepoDb is automatically setting the value of options argument to `SqlBulkCopyOptions.KeepIdentity` when calling this method and if you have not passed any qualifiers and if your table has an `IDENTITY` primary key column. The same logic will apply if there is no primary key but has an `IDENTITY` column defined in the table.

In addition, when calling this method, the library is creating a pseudo temporary table behind the scene. It requires your user to have the correct privilege to create a table in the database, otherwise a `SqlException` will be thrown.

##### Learnings

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Let us say you are retrieving all in active people from the database.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.Query<Person>(e => e.IsActive == false);
}
```

Then, below is the codes that bulk-delete all those inactive records from the *[dbo].[Sales]* table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.BulkDelete<Person>(people);
}
```

Below is a sample bulk-delete by primary keys.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var primaryKeys = people.Select(e => e.Id);
	var deletedRows = connection.BulkDelete<Person>(primaryKeys);
}
```

Below is a sample bulk-delete by data table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var table = people.AsDataTable();
	var deletedRows = connection.BulkDelete<Person>(table);
}
```

Below is a sample bulk-delete by data reader.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
	{
		var rows = connection.BulkDelete<Person>(reader);
	}
}
```

##### Targetting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var deletedRows = connection.BulkDelete("[dbo].[Person]", people);
}
```

##### Field Qualifiers

By default, this operation is using the primary field (or identity field) as the qualifier. You can override the qualifiers by simply passing the list of `Field` object in the `qualifiers` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.BulkDelete<Person>(people,
		qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

##### Table Hints

To pass a hints, simply write the *TableHints* and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var deletedRows = connection.BulkDelete("[dbo].[Person]",
		people,
		hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/SqlServerTableHints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.BulkDelete("[dbo].[Person]",
		people,
		hints: SqlServerTableHints.NoLock);
}
```

##### Physical Temporary Table

To ensure using a physical pseudo-temporary table, simply pass `true` in the `usePhysicalPseudoTempTable` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.BulkDelete("[dbo].[Person]",
		people,
		usePhysicalPseudoTempTable: true);
}
```

> By using the physical table, it gives you maximum performance over the `#Table` temp table. However, you need to be aware that the table is shared to any calls. So parallelism may fail on this scenario.

##### Passing a Transaction

To pass a transaction on this method, simply create an instance of `IDbConnection` and pass it at the `transaction` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var transaction = connection.BeginTransaction())
	{
		try
		{
			var deletedRows = connection.BulkDelete("[dbo].[Person]",
				people,
				usePhysicalPseudoTempTable: true,
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


