---
layout: page
title: "Getting Started (SQL Server)"
permalink: /tutorials/getting-started
tags: [repodb, tutorial, getting-started, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Preface

First, I am glad and would like to thank you for your interest in learning RepoDb. I hope that you will learn a lot and improve the development experiences while using this library.

RepoDb is here to improve the things and experience when working with the Data Access in .NET space. It is always free and is open for suggestions and improvements.

Enjoy learning!

## Getting Started

RepoDb is a hybrid-ORM library for .NET. You can use the library to work with *SQL Server*, *SQLite*, *MySQL* and *PostgreSQL* Relational Database Management Systems (RDBMS).

The project is hosted at [Github](https://github.com/mikependon/RepoDb/tree/master/RepoDb.SqlServer) and is licensed with [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.html).

This tutorial page is only meant for *SQL Server*. If you wish to learn the other data providers, please click any of the link below.

- [SqLite](/tutorials/getting-started-sqlite)
- [MySql](/tutorials/getting-started-mysql)
- [PostgreSql](/tutorials/getting-started-postgresql)

##### Installation

The library can be installed via Nuget. In your *Package Manager Console*, you can type the codes below.

```csharp
> Install-Package RepoDb.SqlServer
```

Once the installation is complete, call the bootstrapper to initialize all the dependencies for *SQL Server*.

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

If you are to work with Bulk Operations (ie: `BulkDelete`, `BulkInsert`, `BulkMerge` and `BulkUpdate`), you have to install the correct package.

In your *Package Manager Console*, simply type the codes below.

```csharp
> Install-Package RepoDb.SqlServer.BulkOperations
```

## Learning the Basics

In this tutorial, we will use the *C#* as the programming language.

##### Pre-Requisites

We assume that you already have created a test database from your *SQL Server* and also a *C#* project.

Before we proceed, we will create a table and a class model.

##### Create a Table

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

##### Create a Model

```csharp
public class Person
{
	public long Id { get; set; }
	public string Name { get; set; }
	public int Age { get; set; }
	public DateTime CreatedDateUtc { get; set; }
}
```

> The class `model` and database `table` specified above will be used by the samples further on this tutorial.

##### Creating a Record

To create a record, you must use the `Insert` method.

```csharp
var person = new Person
{
	Name = "John Doe",
	Age = 54,
	CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SqlConnection(ConnectionString))
{
	var id = connection.Insert(person);
}
```

> The return value would be the value of `Primary` (or `Identity`) field, otherwise `NULL`.

To insert multiple rows, use the `InsertAll` operation instead.

Let us say you had created a method `GetPeople()` that returns the list of `Person`.

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

Then simply create a list of `Person` and passed it when you call the `InsertAll` method.

```csharp
var people = GetPeople(100);
using (var connection = new SqlConnection(ConnectionString))
{
	var id = connection.InsertAll(people);
}
```

##### Querying a Record

To query a record, you must use the `Query` method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var person = connection.Query<Person>(e => e.Id == 1);
	/* Do the stuffs for person here */
}
```

To query all the rows, use the `QueryAll` method instead.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var people = connection.QueryAll<Person>();
	/* Do the stuffs for the people here */
}
```

##### Merging a Record

To merge a record, you must use the `Merge` method.

```csharp
var person = new Person
{
	Id = 1,
	Name = "John Doe",
	Age = 57,
	CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SqlConnection(ConnectionString))
{
	var id = connection.Merge(person);
}
```

By default, the `Primary` (or `Identity`) field is used as the qualifier. You can also use specify a custom qualifiers.

```csharp
var person = new Person
{
	Name = "John Doe",
	Age = 57,
	CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SqlConnection(ConnectionString))
{
	var id = connection.Merge(person, qualifiers: Field.From("Name"));
}
```

To merge all the rows, use the `MergeAll` method instead.

```csharp
var people = GetPeople(100);
people
	.AsList()
	.ForEach(p => p.Name = $"{p.Name} (Merged)");
using (var connection = new SqlConnection(ConnectionString))
{
	var affectedRecords = connection.MergeAll<Person>(people);
}
```

> The `Merge` method returns the `Primary` (or `Identity`) field value while the `MergeAll` method returns the number of rows affected. Both methods will automatically set back the value of the `Identity` property if present.

##### Deleting a Record

To delete a record, you must use the `Delete` method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var deletedCount = connection.Delete<Person>(1);
}
```

By default, it uses the *Primary* field as the qualifier. You can also use the other field.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var deletedCount = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all the rows, use the `DeleteAll` method instead.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var people = connection.DeleteAll<Person>();
}
```

> Both methods will return the number of rows deleted from the datatabase.

##### Updating a Record

To update a record, you must use the `Update` method.

```csharp
var person = new Person
{
	Id = 1,
	Name = "James Doe",
	Age = 55,
	DateInsertedUtc = DateTime.UtcNow
};
using (var connection = new SqlConnection(ConnectionString))
{
	var deletedCount = connection.Update<Person>(person);
}
```

You can also *Update* via dynamic by targetting certain columns.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var deletedCount = connection.Update("Person", new { Id = 1, Name = "James Doe" });
}
```

To update all the rows, use the `UpdateAll` method instead.

```csharp
var people = GetPeople(100);
people
	.AsList()
	.ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new SqlConnection(ConnectionString))
{
	var affectedRecords = connection.UpdateAll<Person>(people);
}
```

By default, the `Primary` (or `Identity`) field is used as a qualifier. You can also specify your custom qualifiers.

```csharp
var people = GetPeople(100);
people
	.AsList()
	.ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new SqlConnection(ConnectionString))
{
	var affectedRecords = connection.UpdateAll<Person>(people, qualifiers: Field.From("Name"));
}
```

##### Executing a Query

To execute a query use the `ExecuteNonQuery` method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var affectedRecords = connection.ExecuteNonQuery("DELETE FROM [dbo].[Person] WHERE Id = @Id;", new { Id = 1 });
}
```

To execute a query while expecting a result of class object, then use the `ExecuteQuery` method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var people = connection.ExecuteQuery<Person>("SELECT ` FROM [dbo].[Person] ORDER BY Id ASC;");
	/* Do the stuffs for the people here */
}
```

To execute a query while expecting a single result, then use the `ExecuteScalar` method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var maxId = connection.ExecuteQuery<Person>("SELECT MAX(Id) FROM [dbo].[Person];");
}
```

To execute a query while expecting a result of `DbDataReader`, then use the `ExecuteReader` method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
	{
		/* Do the stuffs for the data reader here */
	}
}
```

##### Executing a Stored Procedure

To execute a stored procedure, then you can use any of the `Execute` methods mentioned above, but you have to passed the `CommandType` as `CommandType.StoredProcedure`.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var people = connection.ExecuteQuery<Person>("[dbo].[sp_GetPeople]", commandType: CommandType.StoredProcedure);
}
```

> Beware of *NOT* putting a semi-colon at the end of the calls.

Or, you can direct call using the `EXEC` command. No need to pass the the value of `CommandType.StoredProcedure`.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
	var people = connection.ExecuteQuery<Person>("EXEC [dbo].[sp_GetPeople];");
}
```
