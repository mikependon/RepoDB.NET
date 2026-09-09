---
layout: default
sidebar: operations
title: "BulkUpdate"
permalink: /operation/saphana/bulkupdate
tags: [repodb, tutorial, bulkupdate, orm, hybrid-orm, saphana]
parent: "SAP HANA"
grand_parent: OPERATIONS
---

# BulkUpdate

---

This method updates existing rows in the database in bulk, matched by the defined qualifiers. It is supported for [SAP HANA](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations).

{: .note }
> This page documents the SAP HANA-specific arguments and examples. For the SQL Server implementation, see [BulkUpdate (SQL Server)](/operation/sqlserver/bulkupdate).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkUpdate| Source["Entities /<br/>DataTable /<br/>DbDataReader"]
    Source --> Guard{"Any updateable<br/>field left after<br/>excluding qualifiers?"}
    Guard -->|NO| Skip["Return 0<br/>(no pseudo table created)"]
    Guard -->|YES| Pseudo["Create Pseudo Table<br/>(deterministic name) +<br/>Index on qualifiers"]
    Pseudo --> Loader["Buffered row-by-row<br/>parameterized INSERT"]
    Loader -->|Write| PseudoTable[("Pseudo Table")]
    PseudoTable -->|"UPDATE Target SET (...) =<br/>(SELECT ... FROM Pseudo)<br/>WHERE EXISTS (...)<br/>(correlated subquery, no INSERT step)"| Table[("Target Table")]
    PseudoTable -->|Drop| Cleanup(["Pseudo Table Dropped"])
```

## Use Case

Use this method to update rows against SAP HANA without hand-rolling the row-by-row loop yourself.

{: .important }
> SAP HANA has no native bulk-load API, so writing into the pseudo table is a client-buffered loop of single-row, parameterized `INSERT` statements — one round trip per row. The cascading `UPDATE` against the real table, however, is a single native statement.

A pseudo (staging) table, indexed on the qualifier columns, is created under a deterministic name for every call. The library writes to it row-by-row internally, then cascades the changes to the target table — see [Operations (SAP HANA)](/operation/saphana) for the underlying mechanics and its concurrency caveat.

{: .note }
> SAP HANA has no multi-table `UPDATE ... JOIN`, so the cascading statement assigns every updateable column at once via a correlated subquery (`SET (col1, col2, ...) = (SELECT ...)`), guarded by a matching `WHERE EXISTS` clause — not an `UPDATE ... FROM` join. Staged rows with no matching target row are left as-is, not inserted.

## Special Arguments

The `qualifiers`, `mappings`, `bulkCopyTimeout`, `batchSize` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the fields used to match existing rows, corresponding to the `WHERE EXISTS` correlation. Defaults to the primary or identity column if not specified.

`mappings` (via `SapHanaBulkInsertMapItem`) defines explicit column mappings between the source properties and the destination columns, with an optional `HanaDbType` override per mapping. When omitted, columns are auto-mapped by name (case-insensitive).

`bulkCopyTimeout` overrides the command timeout, in seconds, applied to each row's `INSERT` while staging.

`batchSize` overrides how many rows are buffered client-side between flushes (default `500`) — it does not change the number of round trips.

`pseudoTableType` (via [SapHanaBulkImportPseudoTableType](/enumeration/saphana/saphanabulkimportpseudotabletype)) controls the kind of staging table used internally.

{: .note }
> If every staged field is also a qualifier (nothing left to actually update), the operation returns `0` immediately — no pseudo table is created at all.

## Usability

Given a list of `Person` models, the following example bulk-updates rows in the `Person` table.

```csharp
using (var connection = new HanaConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people);
}
```

To specify a batch size:

```csharp
using (var connection = new HanaConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people, batchSize: 100);
}
```

{: .note }
> `batchSize` only controls the client-side buffer; each row is still its own round trip.

#### DataTable

```csharp
using (var connection = new HanaConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var updatedRows = connection.BulkUpdate("\"Person\"", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new HanaConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("\"Person\"");
    using (var destinationConnection = new HanaConnection(destinationConnectionString))
    {
        var updatedRows = destinationConnection.BulkUpdate("\"Person\"", result,
            qualifiers: Field.From("Name"));
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new HanaConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM \"Person\" WHERE \"Age\" > 18"))
    {
        using (var destinationConnection = new HanaConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkUpdate("\"Person\"", reader);
        }
    }
}
```

To bulk-update via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new HanaConnection(connectionString))
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
using (var connection = new HanaConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate<Person>(people,
        qualifiers: e => new { e.Name });
}
```

{: .important }
> Use indexed columns from the target table as qualifiers to maximize performance.

## Column Mappings

Add column mappings using the `SapHanaBulkInsertMapItem` class.

```csharp
var mappings = new List<SapHanaBulkInsertMapItem>();

// Add the mappings
mappings.Add(new SapHanaBulkInsertMapItem("SourceId", "DestinationId"));
mappings.Add(new SapHanaBulkInsertMapItem("SourceName", "DestinationName"));
mappings.Add(new SapHanaBulkInsertMapItem("SourceAge", "DestinationAge"));
mappings.Add(new SapHanaBulkInsertMapItem("SourceCreatedDateUtc", "DestinationCreatedDateUtc"));

// Execute
using (var connection = new HanaConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people,
        mappings: mappings);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new HanaConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate("\"Person\"", people);
}
```

## Async Method

An equivalent [BulkUpdateAsync](/operation/saphana/bulkupdate) method is also available.

```csharp
using (var connection = new HanaConnection(connectionString))
{
    var updatedRows = await connection.BulkUpdateAsync(people);
}
```
