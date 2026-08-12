---
layout: default
sidebar: operations
title: "BulkMerge"
permalink: /operation/db2/bulkmerge
tags: [repodb, tutorial, bulkmerge, orm, hybrid-orm, db2]
parent: "Db2"
grand_parent: OPERATIONS
---

# BulkMerge

---

This method merges all rows from the client application into the database in bulk — inserting new rows and updating existing ones based on the defined qualifiers. It is supported for [Db2](https://www.nuget.org/packages/RepoDb.Db2.BulkOperations).

{: .note }
> This page documents the Db2-specific arguments and examples. For the SQL Server implementation, see [BulkMerge (SQL Server)](/operation/sqlserver/bulkmerge).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkMerge| Source["Entities /<br/>DataTable /<br/>DbDataReader"]
    Source --> Pseudo["Create Pseudo Table<br/>(Physical)"]
    Pseudo --> BulkCopy["DB2BulkCopy"]
    BulkCopy -->|Write| PseudoTable[("Pseudo Table")]
    PseudoTable --> Decision{"identityBehavior ==<br/>ReturnIdentity?"}
    Decision -->|NO| Merge["MERGE INTO ... USING ...<br/>WHEN MATCHED THEN UPDATE<br/>WHEN NOT MATCHED THEN INSERT"]
    Merge --> Table[("Target Table")]
    Decision -->|YES| Snapshot["LEFT JOIN snapshot:<br/>classify matched vs. unmatched rows"]
    Snapshot --> UpdateStep["MERGE ... WHEN MATCHED<br/>THEN UPDATE (matched rows only)"]
    Snapshot --> InsertStep["SELECT id FROM FINAL TABLE(<br/>INSERT ... unmatched rows only)"]
    UpdateStep --> Table
    InsertStep --> Table
    InsertStep -->|"assign identities<br/>back to entities"| Client
    PseudoTable -->|Drop| Cleanup(["Pseudo Table Dropped"])
```

## Use Case

Use this method to merge rows at high speed. It leverages the native bulk operation from the IBM Data Server .NET Provider via the [DB2BulkCopy](https://www.ibm.com/docs/en/db2/11.5?topic=classes-db2bulkcopy-class) class.

For merging 1,000 or more rows, prefer this method over [MergeAll](/operation/mergeall).

A pseudo (staging) table is created under a transaction context. The library writes to it via [BulkInsert](/operation/db2/bulkinsert) internally, then cascades the changes to the target table via a `MERGE` statement — see [Operations (Db2)](/operation/db2) for the underlying mechanics.

## Special Arguments

The `qualifiers`, `mappings`, `bulkCopyTimeout`, `batchSize`, `identityBehavior` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the fields used to match existing rows, corresponding to the `WHERE` clause. Defaults to the primary column if not specified.

`mappings` (via `Db2BulkInsertMapItem`) defines explicit column mappings between the source properties and the destination columns, with an optional `DB2Type` override per mapping. When omitted, columns are auto-mapped by name (case-insensitive).

`bulkCopyTimeout` overrides the command timeout, in seconds.

`batchSize` overrides the number of rows sent to the server per batch. When not set, all items are sent at once.

`identityBehavior` (via [Db2BulkImportIdentityBehavior](/enumeration/db2/db2bulkimportidentitybehavior)) controls whether newly generated identity values are set back on the data entities. Disabled (`KeepIdentity`) by default.

`pseudoTableType` (via [Db2BulkImportPseudoTableType](/enumeration/db2/db2bulkimportpseudotabletype)) controls the kind of staging table used internally.

{: .important }
> Every `pseudoTableType` value currently resolves to `Physical` at runtime — see [Operations (Db2)](/operation/db2#pseudo-table-type) for details.

{: .note }
> The identity column, if any, is always left out of the `INSERT` column list generated for unmatched rows — a brand-new row's identity property is typically an unset default (e.g. `0`), not a real value meant to be inserted as-is.

## Identity Setting Alignment

When `identityBehavior` is `ReturnIdentity`, a single atomic `MERGE` cannot both branch on match and read back generated identities in Db2. The library instead runs three separate statements:

1. A `LEFT JOIN` snapshot between the staging and target table (ordered by a `__RepoDbBulkRowOrder__` identity column added to the staging table) to classify each staged row as matched or unmatched.
2. A `MERGE ... WHEN MATCHED THEN UPDATE` for the matched rows only (skipped entirely if nothing matched).
3. An insert-only statement — again via `SELECT identity FROM FINAL TABLE (INSERT ...)` — for the unmatched rows, reading back their new identities.

{: .important }
> This is not atomic — three round-trips instead of one — and the matched/unmatched classification from step 1 can go stale if another connection modifies the target table before steps 2 and 3 run.

## Usability

Given a list of `Person` models containing both existing and new rows, the following example bulk-merges them into the `Person` table.

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var mergedRows = connection.BulkMerge(people);
}
```

To specify a batch size:

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var mergedRows = connection.BulkMerge(people, batchSize: 100);
}
```

{: .note }
> When `batchSize` is not set, all rows are sent to the server in a single batch.

#### DataTable

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var table = ConvertToDataTable(people);
    var mergedRows = connection.BulkMerge("Person", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new DB2Connection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new DB2Connection(destinationConnectionString))
    {
        var mergedRows = destinationConnection.BulkMerge("Person", result,
            qualifiers: Field.From("LastName", "DateOfBirth"));
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new DB2Connection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM Person WHERE (IsActive = 1)"))
    {
        using (var destinationConnection = new DB2Connection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkMerge("Person", reader);
        }
    }
}
```

To bulk-merge via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(10000);
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var mergedRows = connection.BulkMerge("Person", reader);
    }
}
```

## Field Qualifiers

By default, the primary column is used as the qualifier. To override, pass a list of [Field](/class/field) objects in the `qualifiers` argument.

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

{: .important }
> Use indexed columns from the target table as qualifiers to maximize performance.

## Column Mappings

Add column mappings using the `Db2BulkInsertMapItem` class.

```csharp
var mappings = new List<Db2BulkInsertMapItem>();

// Add the mappings
mappings.Add(new Db2BulkInsertMapItem("SourceId", "DestinationId"));
mappings.Add(new Db2BulkInsertMapItem("SourceName", "DestinationName"));
mappings.Add(new Db2BulkInsertMapItem("SourceIsActive", "DestinationIsActive"));
mappings.Add(new Db2BulkInsertMapItem("SourceDateInsertedUtc", "DestinationDateInsertedUtc"));

// Execute
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people,
        mappings: mappings);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge("Person", people);
}
```

## Async Method

An equivalent [BulkMergeAsync](/operation/db2/bulkmerge) method is also available.

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var mergedRows = await connection.BulkMergeAsync(people);
}
```
