---
layout: default
sidebar: classes
title: "OracleBulkArrayBinder"
description: "An async, array-bind based alternative to OracleBulkCopy, used by the Oracle bulk operations' Async overloads."
permalink: /class/oracle/oraclebulkarraybinder
tags: [repodb, oraclebulkarraybinder, oracle, bulk]
parent: "Oracle"
grand_parent: CLASSES
---

# OracleBulkArrayBinder

---

This class is an async, array-bind based alternative to `OracleBulkCopy`. ODP.NET's `OracleBulkCopy` has no true async equivalent of `OracleBulkCopy.WriteToServer(IDataReader)` — unlike `SqlBulkCopy` for SQL Server, it exposes no genuinely asynchronous write API — so this class issues batched `INSERT INTO ... VALUES (:p0, :p1, ...)` statements with `OracleCommand.ArrayBindCount` set instead, executed via the real `OracleCommand.ExecuteNonQueryAsync(CancellationToken)`.

It is part of [RepoDb.Oracle.BulkOperations](https://www.nuget.org/packages/RepoDb.Oracle.BulkOperations) and implements `IDisposable`.

{: .note }
> This is the class the library itself uses behind the `Async` overloads of [BulkInsert](/operation/oracle/bulkinsert) and the staging-table load of [BulkMerge](/operation/oracle/bulkmerge), [BulkUpdate](/operation/oracle/bulkupdate), [BulkDelete](/operation/oracle/bulkdelete) and [BulkDeleteByKey](/operation/oracle/bulkdeletebykey). Using it directly is only necessary for a custom bulk-write path outside of those operations.

## Properties

| Name | Description |
|:-----|:------------|
| DestinationTableName | The target table to write to. |
| BulkCopyTimeout | The command timeout, in seconds, applied to every batch's `INSERT`. Left at the driver default when not set. |
| BatchSize | The number of rows bound per `INSERT` execution. When not set, it is derived automatically from the column count, capped by ODP.NET's 65,535 bindable-parameter limit. |
| Transaction | The `OracleTransaction` each batch's `INSERT` is enlisted in. |
| ColumnMappings | An `OracleBulkArrayBinderColumnMappingCollection` of explicit source-to-destination column mappings. When left empty, columns are mapped by name. |

## Create a new Instance

```csharp
using (var connection = new OracleConnection(connectionString))
{
    await connection.OpenAsync();

    using var arrayBinder = new OracleBulkArrayBinder(connection)
    {
        DestinationTableName = "Person",
        BatchSize = 1000,
        BulkCopyTimeout = 120
    };
}
```

## Usability

Call `BindArrayAsync()` with either an `IDataReader` or a `DataTable` to write rows. Both overloads return the number of rows written.

Via an `IDataReader` (e.g. [DataEntityDataReader](/class/dataentitydatareader)):

```csharp
using (var connection = new OracleConnection(connectionString))
{
    await connection.OpenAsync();

    var people = GetPeople(100000);
    using var reader = new DataEntityDataReader<Person>(people);
    using var arrayBinder = new OracleBulkArrayBinder(connection)
    {
        DestinationTableName = "Person"
    };

    var writtenRows = await arrayBinder.BindArrayAsync(reader);
}
```

Via a `DataTable`:

```csharp
using (var connection = new OracleConnection(connectionString))
{
    await connection.OpenAsync();

    var table = GetPeopleAsDataTable();
    using var arrayBinder = new OracleBulkArrayBinder(connection)
    {
        DestinationTableName = "Person"
    };

    var writtenRows = await arrayBinder.BindArrayAsync(table);
}
```

With explicit column mappings:

```csharp
using (var connection = new OracleConnection(connectionString))
{
    await connection.OpenAsync();

    using var arrayBinder = new OracleBulkArrayBinder(connection)
    {
        DestinationTableName = "Person"
    };
    arrayBinder.ColumnMappings.Add("FirstName", "FName");
    arrayBinder.ColumnMappings.Add("LastName", "LName");

    var people = GetPeople(100000);
    using var reader = new DataEntityDataReader<Person>(people);
    var writtenRows = await arrayBinder.BindArrayAsync(reader);
}
```

{: .note }
> `BatchSize`, `BulkCopyTimeout` and `Transaction` behave the same way as their equivalents on `OracleBulkCopy` — only the write mechanism (array-bound `INSERT` statements instead of a native bulk-load stream) differs.
