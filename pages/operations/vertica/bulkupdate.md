---
layout: default
sidebar: operations
title: "BulkUpdate"
permalink: /operation/vertica/bulkupdate
tags: [repodb, tutorial, bulkupdate, orm, hybrid-orm, vertica]
parent: "Vertica"
grand_parent: OPERATIONS
---

# BulkUpdate

---

This method updates existing rows in the database in bulk, matched by the defined qualifiers. It is supported for [Vertica](https://www.nuget.org/packages/RepoDb.Vertica.BulkOperations).

{: .note }
> This page documents the Vertica-specific arguments and examples. For the SQL Server implementation, see [BulkUpdate (SQL Server)](/operation/sqlserver/bulkupdate).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkUpdate| Source["Entities /<br/>DataTable /<br/>DbDataReader"]
    Source --> Pseudo["Create Pseudo Table<br/>(Auto/Memory/Physical) +<br/>Index on qualifiers"]
    Pseudo --> Stream["VerticaCopyStream<br/>(COPY ... FROM STDIN)"]
    Stream -->|Write| PseudoTable[("Pseudo Table")]
    PseudoTable -->|"UPDATE Target SET ...<br/>FROM Pseudo WHERE qualifiers match<br/>(no INSERT step)"| Table[("Target Table")]
    PseudoTable -->|Drop| Cleanup(["Pseudo Table Dropped"])
```

## Use Case

Use this method to update rows at high speed. It leverages `VerticaCopyStream`, `Vertica.Data`'s native `COPY ... FROM STDIN` streaming API.

For updating 1,000 or more rows, prefer this method over [UpdateAll](/operation/updateall) — Vertica's `IsMultiStatementExecutable` setting is `false`, so [UpdateAll](/operation/updateall) issues one round trip per row.

A pseudo (staging) table, indexed on the qualifier columns, is created for every call. The library streams into it via a `COPY` load internally, then cascades the changes to the target table via an `UPDATE ... FROM` statement with no `INSERT` step — staged rows with no matching target row are left as-is, not inserted. This reuses the same SQL-generation path as the update half of [BulkMerge](/operation/vertica/bulkmerge#operation-sql-statements), so Vertica's identity-column restrictions apply here too. See [Operations (Vertica)](/operation/vertica) for the underlying mechanics.

## Special Arguments

The `qualifiers`, `mappings`, `bulkCopyTimeout`, `batchSize` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the fields used to match existing rows. Defaults to the primary or identity column if not specified.

`mappings` (via `VerticaBulkInsertMapItem`) defines explicit column mappings between the source properties and the destination columns. When omitted, columns are auto-mapped by name (case-insensitive).

`bulkCopyTimeout` overrides the command timeout, in seconds.

`batchSize` overrides the number of rows sent to the server per batch. When not set, all items are sent at once.

`pseudoTableType` (via [VerticaBulkImportPseudoTableType](/enumeration/vertica/verticabulkimportpseudotabletype)) controls the kind of staging table used internally.

{: .note }
> If every staged field is also a qualifier (nothing left to actually update), the generated `UPDATE` is skipped and the operation returns `0`.

## Caveats

This operation creates a pseudo (staging) table for every call — a per-call, uniquely-named `TABLE` or `GLOBAL TEMPORARY TABLE`, per `pseudoTableType`. The database user must have permission to create tables, or a `VerticaException` will be thrown.

## Usability

Given a list of `Person` models, the following example bulk-updates rows in the `Person` table.

```csharp
using (var connection = new VerticaConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people);
}
```

To specify a batch size:

```csharp
using (var connection = new VerticaConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people, batchSize: 100);
}
```

{: .note }
> When `batchSize` is not set, all rows are sent to the server in a single batch.

#### DataTable

```csharp
using (var connection = new VerticaConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var updatedRows = connection.BulkUpdate("\"Person\"", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new VerticaConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("\"Person\"");
    using (var destinationConnection = new VerticaConnection(destinationConnectionString))
    {
        var updatedRows = destinationConnection.BulkUpdate("\"Person\"", result,
            qualifiers: Field.From("Name"));
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new VerticaConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM \"Person\" WHERE \"Age\" > 18"))
    {
        using (var destinationConnection = new VerticaConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkUpdate("\"Person\"", reader);
        }
    }
}
```

To bulk-update via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new VerticaConnection(connectionString))
{
    var people = GetPeople(10000);
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var updatedRows = connection.BulkUpdate("\"Person\"", reader);
    }
}
```

## Field Qualifiers

By default, the primary or identity column is used as the qualifier. To override, pass a list of [Field](/class/field) objects in the `qualifiers` argument.

```csharp
using (var connection = new VerticaConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate<Person>(people,
        qualifiers: e => new { e.Name });
}
```

{: .important }
> Use indexed columns from the target table as qualifiers to maximize performance.

## Column Mappings

Add column mappings using the `VerticaBulkInsertMapItem` class.

```csharp
var mappings = new List<VerticaBulkInsertMapItem>();

// Add the mappings
mappings.Add(new VerticaBulkInsertMapItem("SourceId", "DestinationId"));
mappings.Add(new VerticaBulkInsertMapItem("SourceName", "DestinationName"));
mappings.Add(new VerticaBulkInsertMapItem("SourceAge", "DestinationAge"));
mappings.Add(new VerticaBulkInsertMapItem("SourceCreatedDateUtc", "DestinationCreatedDateUtc"));

// Execute
using (var connection = new VerticaConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people,
        mappings: mappings);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new VerticaConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate("\"Person\"", people);
}
```

## Async Method

An equivalent [BulkUpdateAsync](/operation/vertica/bulkupdate) method is also available.

```csharp
using (var connection = new VerticaConnection(connectionString))
{
    var updatedRows = await connection.BulkUpdateAsync(people);
}
```
