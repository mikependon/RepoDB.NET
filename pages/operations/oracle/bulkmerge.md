---
layout: default
sidebar: operations
title: "BulkMerge"
permalink: /operation/oracle/bulkmerge
tags: [repodb, tutorial, bulkmerge, orm, hybrid-orm, oracle]
parent: "Oracle"
grand_parent: OPERATIONS
---

# BulkMerge

---

This method merges all rows from the client application into the database in bulk — inserting new rows and updating existing ones based on the defined qualifiers. It is supported for [Oracle](https://www.nuget.org/packages/RepoDb.Oracle.BulkOperations).

{: .note }
> This page documents the Oracle-specific arguments and examples. For the SQL Server implementation, see [BulkMerge (SQL Server)](/operation/sqlserver/bulkmerge).

## Use Case

Use this method to merge rows at high speed. It leverages the native bulk operation from ODP.NET via the [OracleBulkCopy](https://docs.oracle.com/en/database/oracle/oracle-data-access-components/23.9/odpnt/OracleBulkCopyClass.html) class.

For merging 1,000 or more rows, prefer this method over [MergeAll](/operation/mergeall).

A pseudo (staging) table is created under a transaction context. The library writes to it via [BulkInsert](/operation/oracle/bulkinsert) internally, then cascades the changes to the target table via a `MERGE` statement — see [Operations (Oracle)](/operation/oracle) for the underlying mechanics.

## Special Arguments

The `qualifiers`, `mappings`, `bulkCopyTimeout`, `batchSize`, `identityBehavior` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the fields used to match existing rows, corresponding to the `WHERE` clause. Defaults to the primary column if not specified.

`mappings` (via `OracleBulkInsertMapItem`) defines explicit column mappings between the source properties and the destination columns. When omitted, columns are auto-mapped by name (case-insensitive).

`bulkCopyTimeout` overrides the command timeout, in seconds.

`batchSize` overrides the number of rows sent to the server per batch. When not set, all items are sent at once.

`identityBehavior` (via [OracleBulkImportIdentityBehavior](/enumeration/oracle/oraclebulkimportidentitybehavior)) controls whether newly generated identity values are set back on the data entities. Disabled by default.

`pseudoTableType` (via [OracleBulkImportPseudoTableType](/enumeration/oracle/oraclebulkimportpseudotabletype)) controls the kind of staging table used internally.

{: .important }
> Every `pseudoTableType` value currently resolves to `Physical` at runtime — see [Operations (Oracle)](/operation/oracle#pseudo-table-type) for details.

{: .note }
> The identity column, if any, is always left out of the `INSERT` column list generated for unmatched rows — a brand-new row's identity property is typically an unset default (e.g. `0`), not a real value meant to be inserted as-is.

## Identity Setting Alignment

When `identityBehavior` is `ReturnIdentity`, the library adds a `__RepoDb_OrderColumn` column to the staging table to track each entity's position in the source [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0). This ensures the identity values returned from the database are assigned back to the correct entity, regardless of ordering during the underlying `MERGE`.

## Usability

Given a list of `Person` models containing both existing and new rows, the following example bulk-merges them into the `Person` table.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var mergedRows = connection.BulkMerge(people);
}
```

To specify a batch size:

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var mergedRows = connection.BulkMerge(people, batchSize: 100);
}
```

{: .note }
> When `batchSize` is not set, all rows are sent to the server in a single batch.

#### DataTable

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var mergedRows = connection.BulkMerge("Person", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new OracleConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new OracleConnection(destinationConnectionString))
    {
        var mergedRows = destinationConnection.BulkMerge("Person", result,
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
            var rows = destinationConnection.BulkMerge("Person", reader);
        }
    }
}
```

To bulk-merge via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new OracleConnection(connectionString))
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
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge<Person>(people,
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
    var mergedRows = connection.BulkMerge(people,
        mappings: mappings);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge("Person", people);
}
```

## Async Method

An equivalent [BulkMergeAsync](/operation/oracle/bulkmerge) method is also available.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var mergedRows = await connection.BulkMergeAsync(people);
}
```
