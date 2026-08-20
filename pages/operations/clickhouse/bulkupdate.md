---
layout: default
sidebar: operations
title: "BulkUpdate"
permalink: /operation/clickhouse/bulkupdate
tags: [repodb, tutorial, bulkupdate, orm, hybrid-orm, clickhouse]
parent: "ClickHouse"
grand_parent: OPERATIONS
---

# BulkUpdate

---

This method updates existing rows in the database in bulk, matched by the defined qualifiers. It is supported for [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkUpdate| Source["Entities /<br/>DataTable /<br/>DbDataReader"]
    Source --> Pseudo["Create Pseudo Table<br/>(Physical)"]
    Pseudo --> BulkCopy["ClickHouseBulkCopy"]
    BulkCopy -->|Write| PseudoTable[("Pseudo Table")]
    PseudoTable -->|"ALTER TABLE ... UPDATE<br/>WHERE qualifiers<br/>(no anti-join insert)"| Table[("Target Table")]
    PseudoTable -->|Drop| Cleanup(["Pseudo Table Dropped"])
```

## Use Case

Use this method to update rows at high speed. It leverages this package's internal `ClickHouseBulkCopy` class (see [Operations (ClickHouse)](/operation/clickhouse)).

For updating 1,000 or more rows, prefer this method over [UpdateAll](/operation/updateall).

A pseudo (staging) table is created for the call. The library writes to it via `ClickHouseBulkCopy`, then cascades the changes to the target table via a single `ALTER TABLE ... UPDATE` mutation — unlike [BulkMerge](/operation/clickhouse/bulkmerge), staged rows with no matching target row are left as-is, not inserted. See [Operations (ClickHouse)](/operation/clickhouse) for the underlying mechanics.

{: .important }
> The reported result is the number of rows **staged**, not a confirmed post-mutation count — see [No Reliable Affected-Row Count](/operation/clickhouse#no-reliable-affected-row-count).

## Special Arguments

The `qualifiers`, `mappings`, `bulkCopyTimeout`, `batchSize` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the fields used to match existing rows. Defaults to the primary column if not specified.

`mappings` (via `ClickHouseBulkInsertMapItem`) defines explicit column mappings between the source properties and the destination columns. When omitted, columns are auto-mapped by name.

`bulkCopyTimeout` is accepted for API parity with other providers, but currently has no effect.

`batchSize` overrides the number of rows sent to the server per batch. When not set, the driver's own default (100,000) is used.

`pseudoTableType` (via [ClickHouseBulkImportPseudoTableType](/enumeration/clickhouse/clickhousebulkimportpseudotabletype)) controls the kind of staging table used internally.

{: .important }
> Every `pseudoTableType` value currently resolves to `Physical` at runtime — see [Operations (ClickHouse)](/operation/clickhouse#pseudo-table-type) for details.

{: .note }
> There are no identity-related arguments — this operation never generates or reads back identity values. If every staged column is also a qualifier (nothing left to update), the operation short-circuits and returns `0` without touching the database.

## Usability

Given a list of `Person` models, the following example bulk-updates rows in the `Person` table.

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people);
}
```

To specify a batch size:

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people, batchSize: 1000);
}
```

#### DataTable

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var updatedRows = connection.BulkUpdate("Person", table);
}
```

#### DataReader

```csharp
using (var sourceConnection = new ClickHouseConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM Person WHERE (IsActive = true)"))
    {
        using (var destinationConnection = new ClickHouseConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkUpdate("Person", reader);
        }
    }
}
```

To bulk-update via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var people = GetPeople(10000);
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var updatedRows = connection.BulkUpdate("Person", reader);
    }
}
```

## Field Qualifiers

By default, the primary column is used as the qualifier. To override, pass a list of [Field](/class/field) objects in the `qualifiers` argument.

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## Column Mappings

Add column mappings using the `ClickHouseBulkInsertMapItem` class.

```csharp
var mappings = new List<ClickHouseBulkInsertMapItem>();

// Add the mappings
mappings.Add(new ClickHouseBulkInsertMapItem("SourceId", "DestinationId"));
mappings.Add(new ClickHouseBulkInsertMapItem("SourceName", "DestinationName"));

// Execute
using (var connection = new ClickHouseConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people,
        mappings: mappings);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate("Person", people);
}
```

## Async Method

An equivalent `BulkUpdateAsync` method is also available.

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var updatedRows = await connection.BulkUpdateAsync(people);
}
```
