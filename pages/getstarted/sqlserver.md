---
layout: default
sidebar: getstarted
title: "SQL Server"
description: "Learn on how to work with SQL Server databases using RepoDB library."
nav_order: 1
permalink: /tutorial/get-started-sqlserver
tags: [repodb, tutorial, get-started]
parent: GET STARTED
---

# Get Started for SQL Server

---

RepoDB is a hybrid .NET ORM library for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer) RDBMS. The project is hosted at [Github](https://github.com/mikependon/RepoDb/tree/master/RepoDb.SqlServer) and is licensed with [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.html).

## Installation

Install the library via NuGet using the Package Manager Console.

```csharp
> Install-Package RepoDb.SqlServer
```

After installation, call the globalized setup method to initialize all dependencies for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer).

```csharp
GlobalConfiguration
	.Setup()
	.UseSqlServer();
```

For versions prior to 1.13.0, use the bootstrapper below.

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

To use bulk operations ([BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)), install the [RepoDb.SqlServer.BulkOperations](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations) package.

```csharp
> Install-Package RepoDb.SqlServer.BulkOperations
```

For more information, visit the [installation](/tutorial/installation) page.

## Create a Table

The examples below assume the following table exists in the database.

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

## Create a Model

The examples below assume the following model exists in the application.

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

## Creating a Record

To insert a row, use the [Insert](/operation/insert) method.

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

To insert multiple rows, use the [InsertAll](/operation/insertall) operation.

```csharp
var people = GetPeople(100);
using (var connection = new SqlConnection(ConnectionString))
{
    var rowsInserted = connection.InsertAll(people);
}
```

{: .note }
> The [Insert](/operation/insert) method returns the value of identity/primary column, while the [InsertAll](/operation/insertall) method returns the number of rows inserted. Both methods are automatically setting back the value of identity/primary property to the entity model (if present).

## Querying a Record

To query a row, use the [Query](/operation/query) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var person = connection.Query<Person>(e => e.Id == 10045);
    /* Process the result here */
}
```

To query all rows, use the [QueryAll](/operation/queryall) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var people = connection.QueryAll<Person>();
    /* Process the results here */
}
```

## Merging a Record

To merge a row, use the [Merge](/operation/merge) method.

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

By default, the primary column is used as a qualifier. Custom qualifiers can also be specified.

```csharp
var person = new Person
{
    Name = "John Doe",
    Age = 57,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SqlConnection(ConnectionString))
{
    var id = connection.Merge(person, qualifiers: (p => new { p.Name }));
}
```

To merge multiple rows, use the [MergeAll](/operation/mergeall) method.

```csharp
var people = GetPeople(100).AsList();
people
    .ForEach(p => p.Name = $"{p.Name} (Merged)");
using (var connection = new SqlConnection(ConnectionString))
{
    var affectedRecords = connection.MergeAll<Person>(people);
}
```

{: .note }
> The [Merge](/operation/merge) method returns the value of the identity/primary column while the [MergeAll](/operation/mergeall) method returns the number of rows affected. Both methods are automatically setting back the value of identity/primary property to the entity model (if present).

## Deleting a Record

To delete a row, use the [Delete](/operation/delete) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(10045);
}
```

Other columns can also be used as qualifiers.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all rows, use the [DeleteAll](/operation/deleteall) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var deletedRows = connection.DeleteAll<Person>();
}
```

A list of primary keys or models can also be passed for targeted deletion.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var primaryKeys = new [] { 10045, 11001, ..., 12011 };
    var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

{: .note }
> Both the [Delete](/operation/delete) and [DeleteAll](/operation/deleteall) methods return the number of rows affected during the deletion.

## Updating a Record

To update a row, use the [Update](/operation/update) method.

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
    var updatedRows = connection.Update<Person>(person);
}
```

Specific columns can also be targeted using a dynamic update.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var updatedRows = connection.Update("Person", new { Id = 1, Name = "James Doe" });
}
```

To update multiple rows, use the [UpdateAll](/operation/updateall) method.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new SqlConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people);
}
```

By default, the primary column is used as a qualifier. Custom qualifiers can also be specified.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new SqlConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people,
        qualifiers: (p => new { p.Name, p.DateOfBirth }));
}
```

{: .note }
> Both the [Update](/operation/update) and [UpdateAll](/operation/updateall) methods return the number of rows affected during the execution.

## Executing a SQL Text

To execute a non-query statement, use the [ExecuteNonQuery](/operation/executenonquery) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "DELETE FROM [dbo].[Person] WHERE Id = @Id;";
    var affectedRecords = connection.ExecuteNonQuery(sql, new { Id = 1 });
}
```

To execute a query and return mapped objects, use the [ExecuteQuery](/operation/executequery) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "SELECT * FROM [dbo].[Person] ORDER BY Id ASC;";
    var people = connection.ExecuteQuery<Person>(sql);
    /* Process the results here */
}
```

To execute a query and return a scalar value, use the [ExecuteScalar](/operation/executescalar) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "SELECT MAX(Id) FROM [dbo].[Person];";
    var maxId = connection.ExecuteScalar<long>(sql);
}
```

To execute a query and return a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0), use the [ExecuteReader](/operation/executereader) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "SELECT * FROM [dbo].[Person];";
    using (var reader = connection.ExecuteReader(sql))
    {
        /* Process the data reader here */
    }
}
```

## Table-Valued Parameters

To pass a Table-Valued Parameter (TVP), create a `DataTable` and set its name to the name of the User-Defined Type (UDT).

{: .highlight }
> Please follow the Microsoft [guidelines](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/table-valued-parameters) on how to create a TVP and UDT and call it from C#/ADO.NET.

```csharp
var table = new DataTable();
table.TableName = "[dbo].[PersonType]"; // Name of the UDT
// Create the 'table' columns/rows
```

Pass it as a parameter value.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "EXEC [sp_InsertPerson] @PersonTable = @Table;";
    var tables = connection.ExecuteQuery<Person>(sql, new { Table = table })?.AsList();
}
```

## Executing a Stored Procedure

To execute a stored procedure, use any of the execute methods above and pass `CommandType.StoredProcedure` to the `commandType` argument.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var people = connection.ExecuteQuery<Person>("[dbo].[sp_GetPeople]",
        commandType: CommandType.StoredProcedure);
}
```

{: .warning }
> Beware of not putting a semi-colon at the end of the calls.

Alternatively, use the `EXEC` command directly, which does not require the `commandType` argument.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var people = connection.ExecuteQuery<Person>("EXEC [dbo].[sp_GetPeople];");
}
```

{: .note }
> You can also use the types defined at the [Passing of Parameters](/operation/executequery#passing-of-parameters) section when passing a parameter.

## Typed Result Execution

Single-column result sets can be mapped to any .NET CLR type via [ExecuteQuery](/operation/executequery).

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "SELECT Name FROM [dbo].[Person] WHERE Id = @Id;";
    var name = connection.ExecuteQuery<string>(sql, new { Id = 10045 });
}
```

Enumeration types are also supported.

```csharp
public enum Gender
{
    Male,
    Female
}

// Code
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "SELECT Gender FROM [dbo].[Person] WHERE Id = @Id;";
    var name = connection.ExecuteQuery<Gender>(sql, new { Id = 10045 });
}
```

{: .note }
> The result of this operation is an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object.
