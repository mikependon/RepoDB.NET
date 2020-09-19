---
layout: navpage
sidebar: getstarted
title: "Get Started for SQL Server"
description: "Learn on how to work with SQL Server databases using RepoDB library."
permalink: /tutorial/get-started-sqlserver
tags: [repodb, tutorial, get-started, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Get Started for SQL Server

RepoDB is a hybrid .NET ORM library for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer) RDBMS. The project is hosted at [Github](https://github.com/mikependon/RepoDb/tree/master/RepoDb.SqlServer) and is licensed with [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.html).

#### Installation

The library can be installed via Nuget. In your Package Manager Console, type the command below.

```csharp
> Install-Package RepoDb.SqlServer
```

Once installed, call the bootstrapper to initialize all the dependencies for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer).

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

If you are to work with Bulk Operations (i.e.: [BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)), then you must install the [RepoDb.SqlServer.BulkOperations](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations) package.

In your Package Manager Console, simply type the code snippets below.

```csharp
> Install-Package RepoDb.SqlServer.BulkOperations
```

Or visit our [installation](/tutorial/installation) page for more information.

#### Create a Table

```csharp
CREATE TABLE [dbo].[Person]
(
	[Id] [bigint] IDENTITY(1,1) NOT NULL,
	[Name] [nvarchar](128) NOT NULL,
	[Age] [int] NOT NULL,
	[CreatedDateUtc] [datetime2](5) NOT NULL,
	CONSTRAINT [CRIX_Person_Id] PRIMARY KEY CLUSTERED ([Id] ASC) ON [PRIMARY]
)
ON [PRIMARY];
GO
```

#### Create a Model

```csharp
public class Person
{
	public long Id { get; set; }
	public string Name { get; set; }
	public int Age { get; set; }
	public DateTime CreatedDateUtc { get; set; }
}
```

> The class `model` and the database `table` specified above will be used by the samples further on this tutorial.

#### Creating a Record

To create a record, use the [Insert](/operation/insert) method.

```csharp
var person = new Person
{
	Name = "John Doe",
	Age = 54,
	CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var id = connection.Insert(person);
}
```

To insert multiple rows, use the [InsertAll](/operation/insertall) operation instead.

Let us say you had created a method `GetPeople()` that returns a list of `Person`.

```csharp
private IEnumerable<Person> GetPeople(int count = 10)
{
	for (var i = 0; i < count; i++)
	{
		yield return new Person
		{
			Name = $"Person{i}",
			Age = 54,
			CreatedDateUtc = DateTime.UtcNow
		};
	}
}
```

Then simply create a list of `Person` and passed it when you call the [InsertAll](/operation/insertall) method.

```csharp
var people = GetPeople(100).AsList();
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var rowsInserted = connection.InsertAll(people);
}
```

> The [Insert](/operation/insert) method returns the value of the `Primary` (or `Identity`) field while the [InsertAll](/operation/insertall) method returns the number of rows inserted. Both methods are automatically setting back the value of the `PrimaryKey` and/or `Identity` property of the model if present.

#### Querying a Record

To query a record, use the [Query](/operation/query) method.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.Query<Person>(e => e.Id == 10045);
	/* Do the stuffs for person here */
}
```

To query all the rows, use the [QueryAll](/operation/queryall) method.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = connection.QueryAll<Person>();
	/* Do the stuffs for the people here */
}
```

#### Merging a Record

To merge a record, use the [Merge](/operation/merge) method.

```csharp
var person = new Person
{
	Id = 1,
	Name = "John Doe",
	Age = 57,
	CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var id = connection.Merge(person);
}
```

By default, the `Primary` (or `Identity`) field is used as a qualifier. You can also specify the customized qualifiers.

```csharp
var person = new Person
{
	Name = "John Doe",
	Age = 57,
	CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var id = connection.Merge(person, qualifiers: (p => new { p.Name }));
}
```

To merge all the rows, use the [MergeAll](/operation/mergeall) method.

```csharp
var people = GetPeople(100).AsList();
people.ForEach(p => p.Name = $"{p.Name} (Merged)");
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var affectedRecords = connection.MergeAll<Person>(people);
}
```

> The [Merge](/operation/merge) method returns the value of the `Primary` (or `Identity`) field while the [MergeAll](/operation/mergeall) method returns the number of rows affected. Both methods are automatically setting back the value of the `PrimaryKey` and/or `Identity` property of the model if present.

#### Deleting a Record

To delete a record, use the [Delete](/operation/delete) method.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var deletedRows = connection.Delete<Person>(10045);
}
```

By default, it uses the `Primary` (or `Identity`) field as the qualifiers. You can also use the other fields.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var deletedRows = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all the rows, use the [DeleteAll](/operation/deleteall) method.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var deletedRows = connection.DeleteAll<Person>();
}
```

You can also pass the list of primary keys or models to be deleted.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var primaryKeys = new [] { 10045, 11001, ..., 12011 };
	var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

> Both the [Delete](/operation/delete) and [DeleteAll](/operation/deleteall) methods return the number of rows affected during the deletion.

#### Updating a Record

To update a record, use the [Update](/operation/update) method.

```csharp
var person = new Person
{
	Id = 1,
	Name = "James Doe",
	Age = 55,
	DateInsertedUtc = DateTime.UtcNow
};
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var updatedRows = connection.Update<Person>(person);
}
```

You can also dynamically update by targetting the certain columns.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var updatedRows = connection.Update("Person", new { Id = 1, Name = "James Doe" });
}
```

To update all the rows, use the [UpdateAll](/operation/updateall) method.

```csharp
var people = GetPeople(100);
people
	.AsList()
	.ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var updatedRows = connection.UpdateAll<Person>(people);
}
```

By default, the `Primary` (or `Identity`) field is used as a qualifier. You can also specify your custom qualifiers.

```csharp
var people = GetPeople(100);
people
	.AsList()
	.ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var updatedRows = connection.UpdateAll<Person>(people, qualifiers: (p => new { p.Name, p.DateOfBirth }));
}
```

> Both the [Update](/operation/update) and [UpdateAll](/operation/updateall) methods return the number of rows affected during the execution.

#### Executing a SQL Text

To execute a query use the [ExecuteNonQuery](/operation/executenonquery) method.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var affectedRecords = connection.ExecuteNonQuery("DELETE FROM [dbo].[Person] WHERE Id = @Id;", new { Id = 1 });
}
```

To execute a query while expecting a result of class object, use the [ExecuteQuery](/operation/executequery) method.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] ORDER BY Id ASC;");
	/* Do the stuffs for the people here */
}
```

To execute a query while expecting a single result, use the [ExecuteScalar](/operation/executescalar) method.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var maxId = connection.ExecuteQuery<Person>("SELECT MAX(Id) FROM [dbo].[Person];");
}
```

To execute a query while expecting a result of `DbDataReader`, use the [ExecuteReader](/operation/executereader) method.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
	{
		/* Do the stuffs for the data reader here */
	}
}
```

#### Table-Valued Parameters

To execute a Table-Valued Parameter (TVP), create a `DataTable` and set its name equals to the name of the User-Defined Type (UDT).

Please click here to follow the Microsoft [guidelines](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/table-valued-parameters) on how to create a TVP and UDT and call it from C#/ADO.NET.

```csharp
var table = new DataTable();
table.TableName = "[dbo].[PersonType]"; // Name of the UDT
// Create the 'table' columns/rows
```

Then pass it as a value to your argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var tables = connection.ExecuteQuery<Person>("EXEC [sp_InsertPerson] @PersonTable = @Table;",
        new { Table = table })?.AsList();
}
```

#### Executing a Stored Procedure

To execute a stored procedure, you can use any of the `Execute` methods mentioned above, but you have to passed the `CommandType` as `CommandType.StoredProcedure`.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = connection.ExecuteQuery<Person>("[dbo].[sp_GetPeople]", commandType: CommandType.StoredProcedure);
}
```

> Beware of not putting a semi-colon at the end of the calls.

Or, you can direct call using the `EXEC` command. No need to pass the value of the `commandType` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = connection.ExecuteQuery<Person>("EXEC [dbo].[sp_GetPeople];");
}
```
