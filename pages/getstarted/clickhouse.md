---
layout: default
sidebar: getstarted
title: "ClickHouse"
description: "Learn on how to work with ClickHouse databases using RepoDB library."
permalink: /tutorial/get-started-clickhouse
tags: [repodb, tutorial, get-started, orm, hybrid-orm, clickhouse]
parent: GET STARTED
---

# Get Started for ClickHouse

---

RepoDB is a hybrid .NET ORM library for [ClickHouse](https://clickhouse.com/), an open-source, columnar OLAP database. The project is hosted at Github and is licensed with [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.html).

Support ships as two packages:

- [RepoDb.ClickHouse](https://www.nuget.org/packages/RepoDb.ClickHouse) — the core provider, built on [ClickHouse.Driver](https://www.nuget.org/packages/ClickHouse.Driver).
- [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations) — adds `BulkInsert`, `BulkMerge`, `BulkUpdate`, `BulkDelete` and `BulkDeleteByKey`.

## Installation

Install the library via NuGet using the Package Manager Console.

```csharp
> Install-Package RepoDb.ClickHouse
```

After installation, call the globalized setup method to initialize all dependencies for ClickHouse.

```csharp
GlobalConfiguration
    .Setup()
    .UseClickHouse();
```

{: .note }
> Pass `isWaitForMutationsEnabled: true` to `UseClickHouse()` to make `BulkMerge`/`BulkUpdate`/`BulkDelete` block until their mutation actually finishes.

To use bulk operations, install the [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations) package.

```csharp
> Install-Package RepoDb.ClickHouse.BulkOperations
```

## Connection String

```csharp
Host=127.0.0.1;Port=8123;Username=default;Password=YourPassword;Database=RepoDb;Protocol=http;UseCustomDecimals=false;
```

{: .important }
> Always include `UseCustomDecimals=false`, or `Decimal` columns come back as the driver's `ClickHouseDecimal` type instead of a plain .NET `decimal`.

## Create a Table

The examples below assume the following table exists in the database.

```csharp
CREATE TABLE IF NOT EXISTS Person
(
    Id UInt64,
    Name String,
    Age Int32,
    CreatedDateUtc DateTime
)
ENGINE = MergeTree
ORDER BY Id;
```

## Create a Model

The examples below assume the following model exists in the application.

```csharp
public class Person
{
    public ulong Id { get; set; }
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
    Id = 1,
    Name = "John Doe",
    Age = 54,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new ClickHouseConnection(ConnectionString))
{
    connection.Insert(person);
}
```

To insert multiple rows, use the [InsertAll](/operation/insertall) operation.

```csharp
var people = GetPeople(100);
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var rowsInserted = connection.InsertAll(people);
}
```

{: .important }
> ClickHouse has no identity/auto-increment mechanism, so `Id` must always be assigned by the caller before calling [Insert](/operation/insert)/[InsertAll](/operation/insertall).

## Querying a Record

To query a row, use the [Query](/operation/query) method.

```csharp
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var person = connection.Query<Person>(e => e.Id == 1);
    /* Process the result here */
}
```

To query all rows, use the [QueryAll](/operation/queryall) method.

```csharp
using (var connection = new ClickHouseConnection(ConnectionString))
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
using (var connection = new ClickHouseConnection(ConnectionString))
{
    connection.Merge(person);
}
```

{: .important }
> ClickHouse has no native `MERGE`, so [Merge](/operation/merge)/[MergeAll](/operation/mergeall) compile to a plain `INSERT` and rely on the table engine (e.g. `ReplacingMergeTree`) for deduplication — use [BulkMerge](/operation/clickhouse/bulkmerge) instead for an immediate merge.

To merge multiple rows, use the [MergeAll](/operation/mergeall) method.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Merged)");
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var affectedRecords = connection.MergeAll<Person>(people);
}
```

## Deleting a Record

To delete a row, use the [Delete](/operation/delete) method.

```csharp
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(1);
}
```

Other columns can also be used as qualifiers.

```csharp
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all rows, use the [DeleteAll](/operation/deleteall) method.

```csharp
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var deletedRows = connection.DeleteAll<Person>();
}
```

{: .important }
> [Delete](/operation/delete)/[DeleteAll](/operation/deleteall) compile to `ALTER TABLE ... DELETE WHERE ...`, an asynchronous mutation applied later by background merges, so the reported affected-row count is not reliable.

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
using (var connection = new ClickHouseConnection(ConnectionString))
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
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people);
}
```

{: .important }
> [Update](/operation/update)/[UpdateAll](/operation/updateall) compile to `ALTER TABLE ... UPDATE ... WHERE ...` — the same asynchronous mutation as `Delete` — and require a mandatory `WHERE` clause.

## Executing a Query

To execute a non-query statement, use the [ExecuteNonQuery](/operation/executenonquery) method.

```csharp
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var sql = "ALTER TABLE Person DELETE WHERE Id = @Id;";
    var affectedRecords = connection.ExecuteNonQuery(sql, new { Id = 1 });
}
```

To execute a query and return mapped objects, use the [ExecuteQuery](/operation/executequery) method.

```csharp
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var sql = "SELECT * FROM Person ORDER BY Id ASC;";
    var people = connection.ExecuteQuery<Person>(sql);
    /* Process the results here */
}
```

To execute a query and return a scalar value, use the [ExecuteScalar](/operation/executescalar) method.

```csharp
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var sql = "SELECT MAX(Id) FROM Person;";
    var maxId = connection.ExecuteScalar<ulong>(sql);
}
```

To execute a query and return a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0), use the [ExecuteReader](/operation/executereader) method.

```csharp
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var sql = "SELECT * FROM Person ORDER BY Id ASC;";
    using (var reader = connection.ExecuteReader(sql))
    {
        /* Process the data reader here */
    }
}
```

## Typed Result Execution

Single-column result sets can be mapped to any .NET CLR type via [ExecuteQuery](/operation/executequery).

```csharp
using (var connection = new ClickHouseConnection(ConnectionString))
{
    var sql = "SELECT Name FROM Person;";
    var names = connection.ExecuteQuery<string>(sql);
}
```

{: .note }
> Returns an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object.
