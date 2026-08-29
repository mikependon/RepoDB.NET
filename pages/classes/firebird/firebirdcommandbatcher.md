---
layout: default
sidebar: classes
title: "FirebirdCommandBatcher"
description: "An FbBatchCommand-based bulk-copy class for bulk-inserting huge datasets against Firebird, with true asynchronous capability."
permalink: /class/firebird/firebirdcommandbatcher
tags: [repodb, firebirdcommandbatcher, firebird, bulk]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdCommandBatcher

---

This class writes rows to a Firebird table using `FbBatchCommand` — the FirebirdSql.Data.FirebirdClient driver's native ADO.NET batching API — issuing a batched `INSERT INTO ... VALUES (@p0, @p1, ...)` statement per chunk of rows, with true asynchronous execution via `FbBatchCommand.ExecuteNonQueryAsync`.

It is part of [RepoDb.Firebird.BulkOperations](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations) and implements `IDisposable`.

{: .note }
> This is the class the library itself uses to write into the real table (`BulkInsert` without `ReturnIdentity`) and into the pseudo (staging) table backing `BulkInsert` with `ReturnIdentity`, `BulkMerge`, `BulkUpdate`, `BulkDelete` and `BulkDeleteByKey`. Using it directly is only necessary for a custom bulk-write path outside of those operations.

## Properties

| Name | Description |
|:-----|:------------|
| DestinationTableName | The target table to write to. |
| BulkCopyTimeout | The command timeout, in seconds. Accepted for signature symmetry with the other bulk-operations packages, but `FbBatchCommand` has no timeout-equivalent property to apply it to. |
| BatchSize | The number of rows submitted per `FbBatchCommand` round trip. Zero (the default) submits every row in a single round trip. |
| Transaction | The `FbTransaction` each batch's `INSERT` is executed under. |
| ColumnMappings | A [FirebirdCommandBatcherColumnMappingCollection](/class/firebird/firebirdcommandbatchercolumnmappingcollection) of explicit source-to-destination column mappings. When left empty, columns are mapped by name. |

## Create a new Instance

```csharp
using (var connection = new FbConnection(connectionString))
{
    connection.Open();

    using var batcher = new FirebirdCommandBatcher(connection)
    {
        DestinationTableName = "\"Person\"",
        BatchSize = 1000
    };
}
```

## Usability

Call `WriteToServer()`/`WriteToServerAsync()` with either an `IDataReader` or a `DataTable` to write rows. Both overloads return the number of rows written.

Via an `IDataReader` (e.g. [DataEntityDataReader](/class/dataentitydatareader)):

```csharp
using (var connection = new FbConnection(connectionString))
{
    connection.Open();

    var people = GetPeople(100000);
    using var reader = new DataEntityDataReader<Person>(people);
    using var batcher = new FirebirdCommandBatcher(connection)
    {
        DestinationTableName = "\"Person\""
    };

    var writtenRows = await batcher.WriteToServerAsync(reader);
}
```

Via a `DataTable`:

```csharp
using (var connection = new FbConnection(connectionString))
{
    connection.Open();

    var table = GetPeopleAsDataTable();
    using var batcher = new FirebirdCommandBatcher(connection)
    {
        DestinationTableName = "\"Person\""
    };

    var writtenRows = await batcher.WriteToServerAsync(table);
}
```

With explicit column mappings:

```csharp
using (var connection = new FbConnection(connectionString))
{
    connection.Open();

    using var batcher = new FirebirdCommandBatcher(connection)
    {
        DestinationTableName = "\"Person\""
    };
    batcher.ColumnMappings.Add("FirstName", "FName");
    batcher.ColumnMappings.Add("LastName", "LName");

    var people = GetPeople(100000);
    using var reader = new DataEntityDataReader<Person>(people);
    var writtenRows = await batcher.WriteToServerAsync(reader);
}
```

{: .note }
> `BatchSize` and `Transaction` behave the same way as their equivalents on the other providers' bulk-write primitives (e.g. [Db2BulkArrayBinder](/class/db2/db2bulkarraybinder)) — only the write mechanism (`FbBatchCommand` instead of array-bound `INSERT` statements) differs. Each mapping's destination column can bind with an explicit [FbDbType](https://www.nuget.org/packages/FirebirdSql.Data.FirebirdClient) via [FirebirdCommandBatcherMapItem](/class/firebird/firebirdcommandbatchermapitem), though this is rarely needed — Firebird's DSQL layer determines a bind parameter's wire format from the destination column's actual server-side type, not the client-declared one.
