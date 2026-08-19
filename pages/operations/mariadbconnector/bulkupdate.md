---
layout: default
sidebar: operations
title: "BulkUpdate"
permalink: /operation/mariadbconnector/bulkupdate
tags: [repodb, tutorial, bulkupdate, orm, hybrid-orm, mariadb, mariadbconnector]
parent: "MariaDbConnector"
grand_parent: OPERATIONS
---

# BulkUpdate

---

This method updates existing rows in the database in bulk, matched by the defined qualifiers. It is supported for [RepoDb.MariaDbConnector.BulkOperations](https://www.nuget.org/packages/RepoDb.MariaDbConnector.BulkOperations), targeting the [MySqlConnector](https://www.nuget.org/packages/MySqlConnector)-based driver.

{: .note }
> This page documents the `RepoDb.MariaDbConnector` (`MySqlConnector`-based) arguments and examples. For the `MySql.Data`-based implementation, see [BulkUpdate (MariaDb)](/operation/mariadb/bulkupdate); for MySqlConnector's own MySQL provider, see [BulkUpdate (MySqlConnector)](/operation/mysqlconnector/bulkupdate).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkUpdate| Source["Entities /<br/>DataTable /<br/>DbDataReader"]
    Source --> Pseudo["Create Pseudo Table<br/>(Physical)"]
    Pseudo --> BulkCopy["MariaDbBulkCopy"]
    BulkCopy -->|Write| PseudoTable[("Pseudo Table")]
    PseudoTable -->|"UPDATE ... INNER JOIN<br/>ON qualifiers<br/>(no anti-join insert)"| Table[("Target Table")]
    PseudoTable -->|Drop| Cleanup(["Pseudo Table Dropped"])
```

## Use Case

Use this method to update rows at high speed. It leverages `RepoDb.Connector.MariaDbConnector`'s own `MariaDbBulkCopy` class, built on top of [MySqlConnector](https://www.nuget.org/packages/MySqlConnector).

For updating 1,000 or more rows, prefer this method over [UpdateAll](/operation/updateall).

A pseudo (staging) table is created for the call. The library writes to it via [BulkInsert](/operation/mariadbconnector/bulkinsert) internally, then cascades the changes to the target table via a single `UPDATE ... INNER JOIN` statement — unlike [BulkMerge](/operation/mariadbconnector/bulkmerge), staged rows with no matching target row are left as-is, not inserted. See [Operations (MariaDbConnector)](/operation/mariadbconnector) for the underlying mechanics.

## Special Arguments

The `qualifiers`, `mappings`, `bulkCopyTimeout`, `batchSize` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the fields used to match existing rows, corresponding to the join's `ON` clause. Defaults to the primary column if not specified.

`mappings` (via `MariaDbBulkInsertMapItem`) defines explicit column mappings between the source properties and the destination columns. When omitted, columns are auto-mapped by name (case-insensitive).

`bulkCopyTimeout` overrides the command timeout, in seconds.

`batchSize` overrides the number of rows sent to the server per batch. When not set, all items are sent at once.

`pseudoTableType` (via [MariaDbBulkImportPseudoTableType](/enumeration/mariadb/mariadbconnector/mariadbbulkimportpseudotabletype)) controls the kind of staging table used internally.

{: .important }
> Every `pseudoTableType` value currently resolves to `Physical` at runtime — see [Operations (MariaDbConnector)](/operation/mariadbconnector#pseudo-table-type) for details.

{: .note }
> There are no identity-related arguments — this operation never generates or reads back identity values. If every staged column is also a qualifier (nothing left to update), the operation short-circuits and returns `0` without touching the database.

## Usability

Given a list of `Person` models, the following example bulk-updates rows in the `Person` table.

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people);
}
```

To specify a batch size:

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people, batchSize: 100);
}
```

{: .note }
> When `batchSize` is not set, all rows are sent to the server in a single batch.

#### DataTable

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var updatedRows = connection.BulkUpdate("Person", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new MariaDbConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new MariaDbConnection(destinationConnectionString))
    {
        var updatedRows = destinationConnection.BulkUpdate("Person", result,
            qualifiers: Field.From("LastName", "DateOfBirth"));
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new MariaDbConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM Person WHERE (IsActive = 1)"))
    {
        using (var destinationConnection = new MariaDbConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkUpdate("Person", reader);
        }
    }
}
```

To bulk-update via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new MariaDbConnection(connectionString))
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
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

{: .important }
> Use indexed columns from the target table as qualifiers to maximize performance.

## Column Mappings

Add column mappings using the `MariaDbBulkInsertMapItem` class.

```csharp
var mappings = new List<MariaDbBulkInsertMapItem>();

// Add the mappings
mappings.Add(new MariaDbBulkInsertMapItem("SourceId", "DestinationId"));
mappings.Add(new MariaDbBulkInsertMapItem("SourceName", "DestinationName"));
mappings.Add(new MariaDbBulkInsertMapItem("SourceIsActive", "DestinationIsActive"));
mappings.Add(new MariaDbBulkInsertMapItem("SourceDateInsertedUtc", "DestinationDateInsertedUtc"));

// Execute
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people,
        mappings: mappings);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate("Person", people);
}
```

## Async Method

An equivalent [BulkUpdateAsync](/operation/mariadbconnector/bulkupdate) method is also available.

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var updatedRows = await connection.BulkUpdateAsync(people);
}
```
