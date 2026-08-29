---
layout: default
sidebar: operations
title: "BulkDelete"
permalink: /operation/firebird/bulkdelete
tags: [repodb, tutorial, bulkdelete, orm, hybrid-orm, firebird]
parent: "Firebird"
grand_parent: OPERATIONS
---

# BulkDelete

---

This method deletes rows from the database in bulk, matched by the defined qualifiers. It is supported for [Firebird](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations). Firebird also has a dedicated [BulkDeleteByKey](/operation/firebird/bulkdeletebykey) operation for deleting by primary key.

{: .note }
> This page documents the Firebird-specific arguments and examples. For the SQL Server implementation, see [BulkDelete (SQL Server)](/operation/sqlserver/bulkdelete).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkDelete| Source["Entities /<br/>DataTable /<br/>DbDataReader"]
    Source --> Pseudo["Create Pseudo Table<br/>(Auto/Memory/Physical) +<br/>Index on qualifiers"]
    Pseudo --> Batcher["FirebirdCommandBatcher<br/>(FbBatchCommand)"]
    Batcher -->|Write| PseudoTable[("Pseudo Table<br/>(qualifier columns only)")]
    PseudoTable -->|"DELETE FROM Target T<br/>WHERE EXISTS (SELECT 1 FROM<br/>Pseudo S WHERE qualifiers match)"| Table[("Target Table")]
    PseudoTable -->|Drop| Cleanup(["Pseudo Table Dropped"])
```

## Use Case

Use this method to delete rows at high speed. It leverages `FbBatchCommand`, the FirebirdSql.Data.FirebirdClient driver's native ADO.NET batching API, via [FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher).

For deleting 1,000 or more rows, prefer this method over [DeleteAll](/operation/deleteall) — Firebird's `IsMultiStatementExecutable` setting is `false`, so [DeleteAll](/operation/deleteall) issues one round trip per row when deleting by a list of keys.

A pseudo (staging) table, containing only the qualifier columns and indexed on them, is created for every call. The library writes to it via [FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher) internally, then cascades the deletions to the target table via a correlated `EXISTS` subquery — see [Operations (Firebird)](/operation/firebird) for the underlying mechanics.

## Special Arguments

The `qualifiers`, `bulkCopyTimeout`, `batchSize` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the fields used to match existing rows. Defaults to the primary or identity column if not specified.

`bulkCopyTimeout` overrides the command timeout, in seconds. `FbBatchCommand` has no timeout-equivalent property, so this argument currently has nothing to apply to.

`batchSize` overrides the number of rows sent to the server per batch. When not set, all items are sent at once.

`pseudoTableType` (via [FirebirdBulkImportPseudoTableType](/enumeration/firebird/firebirdbulkimportpseudotabletype)) controls the kind of staging table used internally.

## Caveats

This operation creates a pseudo (staging) table for every call — a per-call, uniquely-named `TABLE` or `GLOBAL TEMPORARY TABLE`, per `pseudoTableType`. The database user must have permission to create tables, or an `FbException` will be thrown.

{: .note }
> Every step (creating the pseudo table and its index, writing to it, the cascading `DELETE`, and dropping the pseudo table) is executed against the same `transaction` argument when one is supplied — since Firebird's DDL is itself transactional, passing an explicit `FbTransaction` makes the whole pipeline atomic (a rollback undoes the pseudo table creation too). Without one, each step runs under its own implicit transaction, so a failure partway through can leave an orphaned pseudo table behind.

## Usability

The following example retrieves all inactive people, then bulk-deletes them from the `Person` table.

```csharp
using (var connection = new FbConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = connection.BulkDelete<Person>(people);
}
```

To specify a batch size:

```csharp
using (var connection = new FbConnection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people, batchSize: 100);
}
```

{: .note }
> When `batchSize` is not set, all rows are sent to the server in a single batch.

#### DataTable

```csharp
using (var connection = new FbConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var deletedRows = connection.BulkDelete("\"Person\"", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new FbConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("\"Person\"");
    using (var destinationConnection = new FbConnection(destinationConnectionString))
    {
        var deletedRows = destinationConnection.BulkDelete("\"Person\"", result);
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new FbConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM \"Person\""))
    {
        using (var destinationConnection = new FbConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkDelete("\"Person\"", reader);
        }
    }
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new FbConnection(connectionString))
{
    var deletedRows = connection.BulkDelete("\"Person\"", people);
}
```

## Field Qualifiers

By default, the primary or identity column is used as the qualifier. To override, pass a list of [Field](/class/field) objects in the `qualifiers` argument.

```csharp
using (var connection = new FbConnection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people,
        qualifiers: e => new { e.Name });
}
```

{: .important }
> Use indexed columns from the target table as qualifiers to maximize performance.

## Async Method

An equivalent [BulkDeleteAsync](/operation/firebird/bulkdelete) method is also available.

```csharp
using (var connection = new FbConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = await connection.BulkDeleteAsync<Person>(people);
}
```
