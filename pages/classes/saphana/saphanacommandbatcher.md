---
layout: default
sidebar: classes
title: "SapHanaCommandBatcher"
description: "A HanaCommand-based row-by-row batch-insert class for SAP HANA, offering true asynchronous execution where HanaBulkCopy has none."
permalink: /class/saphana/saphanacommandbatcher
tags: [repodb, saphanacommandbatcher, saphana, bulk]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaCommandBatcher

---

This class writes rows to a SAP HANA table by executing a prepared `INSERT INTO ... VALUES (:p0, :p1, ...)` `HanaCommand` once per row, following the same conventions (`DestinationTableName`, `BatchSize`, `Transaction`, `ColumnMappings`) as `HanaBulkCopy`.

It is part of [RepoDb.SapHana.BulkOperations](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations) and implements `IDisposable`.

{: .note }
> `HanaBulkCopy` has no native async API, so the library's own async bulk operations (`BulkInsertAsync`, `BulkMergeAsync`, `BulkUpdateAsync`, `BulkDeleteAsync`, `BulkDeleteByKeyAsync`) use this class instead — giving genuine asynchronous execution (`HanaCommand.ExecuteNonQueryAsync` per row) rather than a synchronous `HanaBulkCopy` call offloaded onto a thread-pool thread via `Task.Run`. The sync operations still use `HanaBulkCopy` directly. Using this class directly is only necessary for a custom bulk-write path outside of those operations.

## Properties

| Name | Description |
|:-----|:------------|
| DestinationTableName | The target table name, already quoted as `HanaBulkCopy.DestinationTableName` would expect. |
| TableName | The target table name, unquoted, used to look up each destination column's actual type so its parameter's `HanaDbType` can be pre-declared — see the remarks on `AddParameters` for why this is required for `decimal` columns. |
| BulkCopyTimeout | The command timeout, in seconds. Zero uses the provider default. |
| BatchSize | The number of rows executed against a single prepared `HanaCommand` before it is disposed and a fresh one is prepared. Zero (the default) reuses one command for every row. |
| Transaction | The `HanaTransaction` each row is executed under. |
| ColumnMappings | A [SapHanaCommandBatcherColumnMappingCollection](/class/saphana/saphanacommandbatchercolumnmappingcollection) of explicit source-to-destination column mappings. When left empty, columns are mapped by name. |

## Create a new Instance

```csharp
using (var connection = new HanaConnection(connectionString))
{
    connection.Open();

    using var batcher = new SapHanaCommandBatcher(connection)
    {
        DestinationTableName = "\"Person\"",
        TableName = "Person",
        BatchSize = 1000
    };
}
```

## Usability

Call `WriteToServer()`/`WriteToServerAsync()` with either an `IDataReader` or a `DataRow[]` to write rows. Both overloads return the number of rows written.

Via an `IDataReader` (e.g. [DataEntityDataReader](/class/dataentitydatareader)):

```csharp
using (var connection = new HanaConnection(connectionString))
{
    connection.Open();

    var people = GetPeople(100000);
    using var reader = new DataEntityDataReader<Person>(people);
    using var batcher = new SapHanaCommandBatcher(connection)
    {
        DestinationTableName = "\"Person\"",
        TableName = "Person"
    };

    var writtenRows = await batcher.WriteToServerAsync(reader);
}
```

Via a `DataRow[]`:

```csharp
using (var connection = new HanaConnection(connectionString))
{
    connection.Open();

    var rows = GetPeopleAsDataTable().Select();
    using var batcher = new SapHanaCommandBatcher(connection)
    {
        DestinationTableName = "\"Person\"",
        TableName = "Person"
    };

    var writtenRows = await batcher.WriteToServerAsync(rows);
}
```

With explicit column mappings:

```csharp
using (var connection = new HanaConnection(connectionString))
{
    connection.Open();

    using var batcher = new SapHanaCommandBatcher(connection)
    {
        DestinationTableName = "\"Person\"",
        TableName = "Person"
    };
    batcher.ColumnMappings.Add("FirstName", "FName");
    batcher.ColumnMappings.Add("LastName", "LName");

    var people = GetPeople(100000);
    using var reader = new DataEntityDataReader<Person>(people);
    var writtenRows = await batcher.WriteToServerAsync(reader);
}
```

{: .note }
> `BatchSize` and `Transaction` behave the same way as their equivalents on the other providers' bulk-write primitives (e.g. [FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher)) — only the write mechanism (a prepared `HanaCommand` re-executed per row instead of a single array-bound or bulk-copy call) differs. Each mapping's destination column can bind with an explicit `HanaDbType` via [SapHanaBulkInsertMapItem](/class/saphana/saphanabulkinsertmapitem), the same mapping type `HanaBulkCopy` usage in this package binds against — though this is rarely needed since a `decimal` column's type is already resolved through `TableName`.
