---
layout: default
sidebar: getstarted
title: "Vertica"
description: "Learn on how to work with Vertica databases using RepoDB library."
permalink: /tutorial/get-started-vertica
tags: [repodb, tutorial, get-started, orm, hybrid-orm, vertica]
parent: GET STARTED
---

# Get Started for Vertica

---

RepoDB is a hybrid .NET ORM library for [Vertica](https://www.nuget.org/packages/RepoDb.Vertica) Database. The project is hosted at [Github](https://github.com/mikependon/RepoDb/tree/master/RepoDb.Vertica) and is licensed with [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.html).

Support ships as two packages:

- [RepoDb.Vertica](https://www.nuget.org/packages/RepoDb.Vertica) — the core provider, built on [Vertica.Data](https://www.nuget.org/packages/Vertica.Data).
- [RepoDb.Vertica.BulkOperations](https://www.nuget.org/packages/RepoDb.Vertica.BulkOperations) — adds `BulkInsert`, `BulkMerge`, `BulkUpdate`, `BulkDelete` and `BulkDeleteByKey`.

## Installation

Install the library via NuGet using the Package Manager Console.

```csharp
> Install-Package RepoDb.Vertica
```

After installation, call the globalized setup method to initialize all dependencies for Vertica.

```csharp
GlobalConfiguration
    .Setup()
    .UseVertica();
```

{: .note }
> Pass `useInvariantCulture: true` to `UseVertica()` if the machine's culture formats time without colons (e.g. `en-DK`), which can otherwise corrupt date parameter values — this sets `CultureInfo.CurrentCulture` process-wide.

To use bulk operations (`BulkDelete`, `BulkDeleteByKey`, `BulkInsert`, `BulkMerge` and `BulkUpdate` — see [Operations (Vertica)](/operation/vertica)), install the [RepoDb.Vertica.BulkOperations](https://www.nuget.org/packages/RepoDb.Vertica.BulkOperations) package.

```csharp
> Install-Package RepoDb.Vertica.BulkOperations
```

## Create a Table

The examples below assume the following table exists in the database.

```csharp
CREATE TABLE "Person"
(
    "Id" IDENTITY(1, 1),
    "Name" VARCHAR(128),
    "Age" INTEGER,
    "CreatedDateUtc" TIMESTAMP
);
```

## Create a Model

The examples below assume the following model exists in the application.

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    public long Age { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

{: .note }
> Vertica's `SMALLINT`/`INTEGER`/`BIGINT` are all synonyms for one 8-byte integer reported as `long`, so map integer-looking columns as `long` rather than `int` to avoid a cast failure.

## Creating a Record

To insert a row, use the [Insert](/operation/insert) method.

```csharp
var person = new Person
{
    Name = "John Doe",
    Age = 54,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new VerticaConnection(ConnectionString))
{
    var id = connection.Insert(person);
}
```

To insert multiple rows, use the [InsertAll](/operation/insertall) operation.

```csharp
var people = GetPeople(100);
using (var connection = new VerticaConnection(ConnectionString))
{
    var rowsInserted = connection.InsertAll(people);
}
```

{: .note }
> [InsertAll](/operation/insertall) still batches rows into one multi-row `INSERT ... VALUES (...), (...)` statement despite `IsMultiStatementExecutable` being `false`, but [MergeAll](/operation/mergeall)/[UpdateAll](/operation/updateall) issue one round trip per row and reject a `batchSize` greater than `1`.

## Querying a Record

To query a row, use the [Query](/operation/query) method.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var person = connection.Query<Person>(e => e.Id == 1);
    /* Process the result here */
}
```

To query all rows, use the [QueryAll](/operation/queryall) method.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var people = connection.QueryAll<Person>();
    /* Process the results here */
}
```

{: .note }
> Vertica has no table-hint syntax — passing a `hints` argument to any operation throws a `NotSupportedException`.

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
using (var connection = new VerticaConnection(ConnectionString))
{
    var id = connection.Merge(person);
}
```

By default, the primary or identity column is used as a qualifier. Custom qualifiers can also be specified.

```csharp
var person = new Person
{
    Name = "John Doe",
    Age = 57,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new VerticaConnection(ConnectionString))
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
using (var connection = new VerticaConnection(ConnectionString))
{
    var affectedRecords = connection.MergeAll<Person>(people);
}
```

{: .important }
> Vertica refuses `MERGE` against a table with an `IDENTITY`/`AUTO_INCREMENT` column, so [Merge](/operation/merge)/[MergeAll](/operation/mergeall) instead compile to an `UPDATE` followed by an `INSERT ... WHERE NOT EXISTS (...)` — verify this against a live instance before relying on it in production.

## Deleting a Record

To delete a row, use the [Delete](/operation/delete) method.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(1);
}
```

Other columns can also be used as qualifiers.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all rows, use the [DeleteAll](/operation/deleteall) method.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var deletedRows = connection.DeleteAll<Person>();
}
```

{: .note }
> [Delete](/operation/delete) and [DeleteAll](/operation/deleteall) both return the number of rows affected.

## Updating a Record

To update a row, use the [Update](/operation/update) method.

```csharp
var person = new Person
{
    Id = 1,
    Name = "James Doe",
    Age = 55,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new VerticaConnection(ConnectionString))
{
    var updatedRows = connection.Update<Person>(person);
}
```

To update multiple rows, use the [UpdateAll](/operation/updateall) method.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new VerticaConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people);
}
```

{: .note }
> [Update](/operation/update) and [UpdateAll](/operation/updateall) both return the number of rows affected.

## Executing a Query

To execute a non-query statement, use the [ExecuteNonQuery](/operation/executenonquery) method.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var sql = "DELETE FROM \"Person\" WHERE \"Id\" = @Id";
    var affectedRecords = connection.ExecuteNonQuery(sql, new { Id = 1 });
}
```

To execute a query and return mapped objects, use the [ExecuteQuery](/operation/executequery) method.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var sql = "SELECT * FROM \"Person\" ORDER BY \"Id\" ASC";
    var people = connection.ExecuteQuery<Person>(sql);
    /* Process the results here */
}
```

To execute a query and return a scalar value, use the [ExecuteScalar](/operation/executescalar) method.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var sql = "SELECT MAX(\"Id\") FROM \"Person\"";
    var maxId = connection.ExecuteScalar<long>(sql);
}
```

To execute a query and return a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0), use the [ExecuteReader](/operation/executereader) method.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var sql = "SELECT * FROM \"Person\"";
    using (var reader = connection.ExecuteReader(sql))
    {
        /* Process the data reader here */
    }
}
```

## Executing a Stored Procedure

To execute a stored procedure, use any of the execute methods above and pass `CommandType.StoredProcedure` to the `commandType` argument.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var people = connection.ExecuteQuery<Person>("GetPeople",
        commandType: CommandType.StoredProcedure);
}
```

{: .warning }
> Beware of not putting a semi-colon at the end of the calls.

Alternatively, use the `CALL` command directly, which does not require the `commandType` argument.

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var people = connection.ExecuteQuery<Person>("CALL GetPeople()");
}
```

{: .note }
> `CALL` invokes an actual stored procedure (Vertica 10+); a scalar UDF is invoked via `SELECT function_name(...)` instead.

{: .note }
> You can also use the types defined at the [Passing of Parameters](/operation/executequery#passing-of-parameters) section when passing a parameter.

## Typed Result Execution

Single-column result sets can be mapped to any .NET CLR type via [ExecuteQuery](/operation/executequery).

```csharp
using (var connection = new VerticaConnection(ConnectionString))
{
    var sql = "SELECT \"Name\" FROM \"Person\"";
    var names = connection.ExecuteQuery<string>(sql);
}
```

{: .note }
> Returns an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object.
