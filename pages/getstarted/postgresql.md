---
layout: default
sidebar: getstarted
title: "PostgreSQL"
description: "Learn on how to work with PostgreSQL databases using RepoDB library."
nav_order: 2
permalink: /tutorial/get-started-postgresql
tags: [repodb, tutorial, get-started, orm, hybrid-orm, postgresql]
parent: GET STARTED
---

# Get Started for PostgreSQL

---

RepoDB is a hybrid .NET ORM library for [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql) RDBMS. The project is hosted at [Github](https://github.com/mikependon/RepoDb/tree/master/RepoDb.PostgreSql) and is licensed with [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.html).

## Installation

Install the library via NuGet using the Package Manager Console.

```csharp
> Install-Package RepoDb.PostgreSql
```

To use bulk operations ([BinaryBulkDelete](/operation/binarybulkdelete), [BinaryBulkInsert](/operation/binarybulkinsert), [BinaryBulkMerge](/operation/binarybulkmerge) and [BinaryBulkUpdate](/operation/binarybulkupdate)), install the [RepoDb.PostgreSql.BulkOperations](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) package.

```csharp
> Install-Package RepoDb.PostgreSql.BulkOperations
```

After installation, call the globalized setup method to initialize all dependencies for PostgreSql.

```csharp
GlobalConfiguration
	.Setup()
	.UsePostgreSql();
```

For versions prior to 1.13.0, use the bootstrapper below.

```csharp
RepoDb.PostgreSqlBootstrap.Initialize();
```

For more information, visit the [installation](/tutorial/installation) page.

## Create a Table

The examples below assume the following table exists in the database.

```csharp
CREATE TABLE IF NOT EXISTS "Person"
(
    "Id" bigint(20) NOT NULL AUTO_INCREMENT,
    "Name" text,
    "Age" int(11) DEFAULT NULL,
    "CreatedDateUtc" datetime DEFAULT NULL
);
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
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var id = connection.Insert(person);
}
```

To insert multiple rows, use the [InsertAll](/operation/insertall) operation.

```csharp
var people = GetPeople(100);
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var rowsInserted = connection.InsertAll(people);
}
```

{: .note }
> The [Insert](/operation/insert) method returns the value of identity/primary column, while the [InsertAll](/operation/insertall) method returns the number of rows inserted. Both methods are automatically setting back the value of identity/primary property to the entity model (if present).

## Querying a Record

To query a row, use the [Query](/operation/query) method.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var person = connection.Query<Person>(e => e.Id == 1);
    /* Process the result here */
}
```

To query all rows, use the [QueryAll](/operation/queryall) method.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
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
using (var connection = new NpgsqlConnection(ConnectionString))
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
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var id = connection.Merge(person, qualifiers: (p => new { p.Name }));
}
```

To merge multiple rows, use the [MergeAll](/operation/mergeall) method.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Merged)");
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var affectedRecords = connection.MergeAll<Person>(people);
}
```

{: .note }
> The [Merge](/operation/merge) method returns the identity/primary column value, while the [MergeAll](/operation/mergeall) method returns the number of rows affected. Both methods are automatically setting back the value of identity/primary property if present.

## Deleting a Record

To delete a row, use the [Delete](/operation/delete) method.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var deletedCount = connection.Delete<Person>(1);
}
```

Other columns can also be used as qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all rows, use the [DeleteAll](/operation/deleteall) method.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var deletedRows = connection.DeleteAll<Person>();
}
```

A list of primary keys can also be passed for targeted deletion.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var primaryKeys = new [] { 10045, 11001, ..., 12011 };
    var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

{: .note }
> Both the [Delete](/operation/delete) and [DeleteAll](/operation/deleteall) methods return the number of rows affected during the execution.

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
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var updatedRows = connection.Update<Person>(person);
}
```

Specific columns can also be targeted using a dynamic update.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
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
using (var connection = new NpgsqlConnection(ConnectionString))
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
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people,
        qualifiers: (p => new { p.Name }));
}
```

{: .note }
> Both the [Update](/operation/update) and [UpdateAll](/operation/updateall) methods return the number of rows affected during the execution.

## Executing a Query

To execute a non-query statement, use the [ExecuteNonQuery](/operation/executenonquery) method.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var sql = "DELETE FROM \"Person\" WHERE \"Id\" = @Id;";
    var affectedRecords = connection.ExecuteNonQuery(sql, new { Id = 1 });
}
```

To execute a query and return mapped objects, use the [ExecuteQuery](/operation/executequery) method.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var sql = "SELECT * FROM \"Person\" ORDER BY \"Id\" ASC;";
    var people = connection.ExecuteQuery<Person>(sql);
    /* Process the results here */
}
```

To execute a query and return a scalar value, use the [ExecuteScalar](/operation/executescalar) method.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var sql = "SELECT MAX(\"Id\") FROM \"Person\";";
    var maxId = connection.ExecuteQuery<Person>(sql);
}
```

To execute a query and return a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0), use the [ExecuteReader](/operation/executereader) method.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var sql = "SELECT * FROM \"Person\" ORDER BY \"Id\" ASC;";
    using (var reader = connection.ExecuteReader(sql))
    {
        /* Process the data reader here */
    }
}
```

## Typed Result Execution

Single-column result sets can be mapped to any .NET CLR type via [ExecuteQuery](/operation/executequery).

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var sql = "SELECT \"Name\" FROM \"Person\" WHERE \"Id\" = @Id;";
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
```

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
    var sql = "SELECT \"Gender\" FROM \"Person\" WHERE \"Id\" = @Id;";
    var name = connection.ExecuteQuery<Gender>(sql, new { Id = 10045 });
}
```

{: .note }
> The resultset of this operation is an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object.
