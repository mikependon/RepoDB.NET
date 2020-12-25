---
layout: default
sidebar: getstarted
title: "SQL Server"
description: "Learn on how to work with SQL Server databases using RepoDB library."
nav_order: 1
permalink: /tutorial/get-started-sqlserver
tags: [repodb, tutorial, get-started, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Get Started
---

# Get Started for SQL Server

---

RepoDB is a hybrid .NET ORM library for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer) RDBMS. The project is hosted at [Github](https://github.com/mikependon/RepoDb/tree/master/RepoDb.SqlServer) and is licensed with [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.html).

### Installation

The library can be installed via Nuget. In your Package Manager Console, type the command below.

```csharp
> Install-Package RepoDb.SqlServer
```

Once installed, call the bootstrapper to initialize all the dependencies for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer).

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

If you are to work with the bulk operations (i.e.: [BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)), then you must install the [RepoDb.SqlServer.BulkOperations](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations) package.

In your Package Manager Console, simply type the command below to install the target package.

```csharp
> Install-Package RepoDb.SqlServer.BulkOperations
```

Or visit our [installation](/tutorial/installation) page for more information.

### Create a Table

Let us say you have this table on your database.

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

### Create a Model

And you have this model on your application.

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

### Creating a Record

To create a row, use the [Insert](/operation/insert) method.

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

Then simply create a list of `Person` object and pass it when you call the [InsertAll](/operation/insertall) method.

```csharp
var people = GetPeople(100).AsList();
using (var connection = new SqlConnection(ConnectionString))
{
    var rowsInserted = connection.InsertAll(people);
}
```

> The [Insert](/operation/insert) method returns the value of primary or identity column while the [InsertAll](/operation/insertall) method returns the number of rows inserted. Both methods are automatically setting back the value of the primary and/or identity property of the model if present.

### Querying a Record

To query a row, use the [Query](/operation/query) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var person = connection.Query<Person>(e => e.Id == 10045);
    /* Do the stuffs for person here */
}
```

To query all the rows, use the [QueryAll](/operation/queryall) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var people = connection.QueryAll<Person>();
    /* Do the stuffs for the people here */
}
```

### Merging a Record

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

By default, the primary or identity column is used as a qualifier. You can also customized the qualifiers with other columns.

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

To merge all the rows, use the [MergeAll](/operation/mergeall) method.

```csharp
var people = GetPeople(100).AsList();
people.ForEach(p => p.Name = $"{p.Name} (Merged)");
using (var connection = new SqlConnection(ConnectionString))
{
    var affectedRecords = connection.MergeAll<Person>(people);
}
```

> The [Merge](/operation/merge) method returns the value of the primary or identity column while the [MergeAll](/operation/mergeall) method returns the number of rows affected. Both methods are automatically setting back the value of the primary and/or identity property of the model if present.

### Deleting a Record

To delete a row, use the [Delete](/operation/delete) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(10045);
}
```

By default, it uses the primary or identity column as a qualifier, but you can also use the other columns like below.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all the rows, use the [DeleteAll](/operation/deleteall) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var deletedRows = connection.DeleteAll<Person>();
}
```

You can also pass the list of primary keys or models to be deleted.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var primaryKeys = new [] { 10045, 11001, ..., 12011 };
    var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

> Both the [Delete](/operation/delete) and [DeleteAll](/operation/deleteall) methods return the number of rows affected during the deletion.

### Updating a Record

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

You can also dynamically update by targeting the certain columns.

```csharp
using (var connection = new SqlConnection(ConnectionString))
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
using (var connection = new SqlConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people);
}
```

By default, the primary or identity column is used as a qualifier, but you can also specify your custom qualifiers.

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

> Both the [Update](/operation/update) and [UpdateAll](/operation/updateall) methods return the number of rows affected during the execution.

### Executing a SQL Text

To execute a query, use the [ExecuteNonQuery](/operation/executenonquery) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "DELETE FROM [dbo].[Person] WHERE Id = @Id;";
    var affectedRecords = connection.ExecuteNonQuery(sql, new { Id = 1 });
}
```

To execute a query while expecting a result of class object, use the [ExecuteQuery](/operation/executequery) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "SELECT * FROM [dbo].[Person] ORDER BY Id ASC;";
    var people = connection.ExecuteQuery<Person>(sql);
    /* Do the stuffs for the people here */
}
```

To execute a query while expecting a single result, use the [ExecuteScalar](/operation/executescalar) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "SELECT MAX(Id) FROM [dbo].[Person];";
    var maxId = connection.ExecuteQuery<Person>(sql);
}
```

To execute a query while expecting a result of `DbDataReader` object, use the [ExecuteReader](/operation/executereader) method.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "SELECT * FROM [dbo].[Person];";
    using (var reader = connection.ExecuteReader(sql))
    {
        /* Do the stuffs for the data reader here */
    }
}
```

### Table-Valued Parameters

To execute a Table-Valued Parameter (TVP), create a `DataTable` and set its name equals to the name of the User-Defined Type (UDT).

Please follow the Microsoft [guidelines](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/table-valued-parameters) on how to create a TVP and UDT and call it from C#/ADO.NET.

```csharp
var table = new DataTable();
table.TableName = "[dbo].[PersonType]"; // Name of the UDT
// Create the 'table' columns/rows
```

Then pass it as a value to your argument.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "EXEC [sp_InsertPerson] @PersonTable = @Table;";
    var tables = connection.ExecuteQuery<Person>(sql, new { Table = table })?.AsList();
}
```

### Executing a Stored Procedure

To execute a stored procedure, you can use any of the execute methods mentioned above, but you have to passed the `CommandType.StoredProcedure` enumeration value on the `commandType` argument.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var people = connection.ExecuteQuery<Person>("[dbo].[sp_GetPeople]",
        commandType: CommandType.StoredProcedure);
}
```

> Beware of not putting a semi-colon at the end of the calls.

Or, you can direct call using the `EXEC` command. With this, you do not need to pass the value of the `commandType` argument.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var people = connection.ExecuteQuery<Person>("EXEC [dbo].[sp_GetPeople];");
}
```

> You can also use the types defined at the [Passing of Parameters](/operation/executequery#passing-of-parameters) section when passing a parameter.

### Typed Result Execution

You can infer the scalar resultset in any .NET CLR type via [ExecuteQuery](/operation/executequery) operation.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "SELECT Name FROM [dbo].[Person] WHERE Id = @Id;";
    var name = connection.ExecuteQuery<string>(sql, new { Id = 10045 });
}
```

And even with the Enumerations.

```csharp
public enum Gender
{
    Male,
    Female
}
```

Then call it like below.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var sql = "SELECT Gender FROM [dbo].[Person] WHERE Id = @Id;";
    var name = connection.ExecuteQuery<Gender>(sql, new { Id = 10045 });
}
```

> Please be noted that the resultset of this operation is an `IEnumerable<T>` object.