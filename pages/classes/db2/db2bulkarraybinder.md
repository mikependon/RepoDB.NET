---
layout: default
sidebar: classes
title: "Db2BulkArrayBinder"
description: "An async, array-bind based alternative to DB2BulkCopy, used by the Db2 bulk operations for true asynchronous bulk inserts."
permalink: /class/db2/db2bulkarraybinder
tags: [repodb, db2bulkarraybinder, db2, bulk]
parent: "Db2"
grand_parent: CLASSES
---

# Db2BulkArrayBinder

---

This class is an async, array-bind based alternative to `DB2BulkCopy` for bulk inserting huge amounts of data with true asynchronous capability. It issues batched `INSERT INTO ... VALUES (:p0, :p1, ...)` statements with `DB2Command.ArrayBindCount` set, executed via `DB2Command.ExecuteNonQueryAsync(CancellationToken)`.

It is part of [RepoDb.Db2.BulkOperations](https://www.nuget.org/packages/RepoDb.Db2.BulkOperations) and implements `IDisposable`.

## Properties

| Name | Description |
|:-----|:------------|
| DestinationTableName | The target table to write to. When set, an identity column present on the table is automatically excluded from the resolved column mappings. |
| BulkCopyTimeout | The command timeout, in seconds, applied to every batch's `INSERT`. Zero (the default) uses the driver default. |
| BatchSize | The number of rows bound per `INSERT` execution. Zero (the default) auto-sizes it as `Min(1000, 32767 / columnCount)`, keeping every batch within the IBM Data Server .NET Provider's 32,767 bindable-parameters-per-command limit. |
| Transaction | The `DB2Transaction` each batch's `INSERT` is executed under. |
| ColumnMappings | A [Db2BulkArrayBinderColumnMappingCollection](/class/db2/db2bulkarraybindercolumnmappingcollection) of explicit source-to-destination column mappings. When left empty, columns are mapped by name (identity mapping). |

## Create a new Instance

```csharp
using (var connection = new DB2Connection(connectionString))
{
    connection.Open();

    using var arrayBinder = new Db2BulkArrayBinder(connection)
    {
        DestinationTableName = "Person",
        BatchSize = 1000,
        BulkCopyTimeout = 120
    };
}
```

## Usability

Call `WriteToServerAsync()` with either an `IDataReader` or a `DataTable` to write rows. Both overloads return the number of rows written.

Via an `IDataReader` (e.g. [DataEntityDataReader](/class/dataentitydatareader)):

```csharp
using (var connection = new DB2Connection(connectionString))
{
    connection.Open();

    var people = GetPeople(100000);
    using var reader = new DataEntityDataReader<Person>(people);
    using var arrayBinder = new Db2BulkArrayBinder(connection)
    {
        DestinationTableName = "Person"
    };

    var writtenRows = await arrayBinder.WriteToServerAsync(reader);
}
```

Via a `DataTable`:

```csharp
using (var connection = new DB2Connection(connectionString))
{
    connection.Open();

    var table = GetPeopleAsDataTable();
    using var arrayBinder = new Db2BulkArrayBinder(connection)
    {
        DestinationTableName = "Person"
    };

    var writtenRows = await arrayBinder.WriteToServerAsync(table);
}
```

Optionally, restrict a `DataTable` write to rows in a specific `DataRowState` (e.g. only `Added` rows):

```csharp
var writtenRows = await arrayBinder.WriteToServerAsync(table, DataRowState.Added);
```

With explicit column mappings:

```csharp
using (var connection = new DB2Connection(connectionString))
{
    connection.Open();

    using var arrayBinder = new Db2BulkArrayBinder(connection)
    {
        DestinationTableName = "Person"
    };
    arrayBinder.ColumnMappings.Add("FirstName", "FName");
    arrayBinder.ColumnMappings.Add("LastName", "LName");

    var people = GetPeople(100000);
    using var reader = new DataEntityDataReader<Person>(people);
    var writtenRows = await arrayBinder.WriteToServerAsync(reader);
}
```

{: .note }
> `BatchSize`, `BulkCopyTimeout` and `Transaction` behave the same way as their equivalents on `DB2BulkCopy` — only the write mechanism (array-bound `INSERT` statements instead of a native bulk-load stream) differs. Each bound parameter's `DB2Type` comes from an explicit [Db2BulkInsertMapItem](/class/db2/db2bulkinsertmapitem) mapping when provided, otherwise it is inferred from the source column's CLR type via [TypeToDb2TypeResolver](/class/db2/typetodb2typeresolver).
