---
layout: default
sidebar: operations
title: "BulkUpdate"
permalink: /operation/oracle/bulkupdate
tags: [repodb, tutorial, bulkupdate, orm, hybrid-orm, oracle]
parent: "Oracle"
grand_parent: OPERATIONS
---

# BulkUpdate

---

This method updates existing rows in the database in bulk, matched by the defined qualifiers. It is supported for [Oracle](https://www.nuget.org/packages/RepoDb.Oracle.BulkOperations).

{: .note }
> This page documents the Oracle-specific arguments and examples. For the SQL Server implementation, see [BulkUpdate (SQL Server)](/operation/sqlserver/bulkupdate).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkUpdate| Source["Entities /<br/>DataTable /<br/>DbDataReader"]
    Source --> Pseudo["Create Pseudo Table<br/>(Physical)"]
    Pseudo --> BulkCopy["OracleBulkCopy<br/>(direct-path load)"]
    BulkCopy -->|Write| PseudoTable[("Pseudo Table")]
    PseudoTable -->|"MERGE INTO ... USING ...<br/>WHEN MATCHED THEN UPDATE<br/>(no WHEN NOT MATCHED)"| Table[("Target Table")]
    PseudoTable -->|Drop| Cleanup(["Pseudo Table Dropped"])
```

## Use Case

Use this method to update rows at high speed. It leverages the native bulk operation from ODP.NET via the [OracleBulkCopy](https://docs.oracle.com/en/database/oracle/oracle-data-access-components/23.9/odpnt/OracleBulkCopyClass.html) class.

For updating 1,000 or more rows, prefer this method over [UpdateAll](/operation/updateall).

A pseudo (staging) table is created under a transaction context. The library writes to it via [BulkInsert](/operation/oracle/bulkinsert) internally, then cascades the changes to the target table via a `MERGE` statement with no `WHEN NOT MATCHED` branch — staged rows with no matching target row are left as-is, not inserted. See [Operations (Oracle)](/operation/oracle) for the underlying mechanics.

## Special Arguments

The `qualifiers`, `mappings`, `bulkCopyTimeout`, `batchSize` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the fields used to match existing rows, corresponding to the `WHERE` clause. Defaults to the primary column if not specified.

`mappings` (via `OracleBulkInsertMapItem`) defines explicit column mappings between the source properties and the destination columns. When omitted, columns are auto-mapped by name (case-insensitive).

`bulkCopyTimeout` overrides the command timeout, in seconds.

`batchSize` overrides the number of rows sent to the server per batch. When not set, all items are sent at once.

`pseudoTableType` (via [OracleBulkImportPseudoTableType](/enumeration/oracle/oraclebulkimportpseudotabletype)) controls the kind of staging table used internally.

{: .important }
> Every `pseudoTableType` value currently resolves to `Physical` at runtime — see [Operations (Oracle)](/operation/oracle#pseudo-table-type) for details.

## Caveats

This operation creates a pseudo-temporary table internally under a transaction context. The database user must have permission to create tables, or an `OracleException` will be thrown.

## Usability

Given a list of `Person` models, the following example bulk-updates rows in the `Person` table.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people);
}
```

To specify a batch size:

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people, batchSize: 100);
}
```

{: .note }
> When `batchSize` is not set, all rows are sent to the server in a single batch.

#### DataTable

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var updatedRows = connection.BulkUpdate("Person", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new OracleConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new OracleConnection(destinationConnectionString))
    {
        var updatedRows = destinationConnection.BulkUpdate("Person", result,
            qualifiers: Field.From("LastName", "DateOfBirth"));
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new OracleConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM Person WHERE (IsActive = 1)"))
    {
        using (var destinationConnection = new OracleConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkUpdate("Person", reader);
        }
    }
}
```

To bulk-update via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new OracleConnection(connectionString))
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
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

{: .important }
> Use indexed columns from the target table as qualifiers to maximize performance.

## Column Mappings

Add column mappings using the `OracleBulkInsertMapItem` class.

```csharp
var mappings = new List<OracleBulkInsertMapItem>();

// Add the mappings
mappings.Add(new OracleBulkInsertMapItem("SourceId", "DestinationId"));
mappings.Add(new OracleBulkInsertMapItem("SourceName", "DestinationName"));
mappings.Add(new OracleBulkInsertMapItem("SourceIsActive", "DestinationIsActive"));
mappings.Add(new OracleBulkInsertMapItem("SourceDateInsertedUtc", "DestinationDateInsertedUtc"));

// Execute
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people,
        mappings: mappings);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate("Person", people);
}
```

## Async Method

An equivalent [BulkUpdateAsync](/operation/oracle/bulkupdate) method is also available.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var updatedRows = await connection.BulkUpdateAsync(people);
}
```
