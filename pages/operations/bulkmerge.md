---
layout: default
sidebar: operations
title: "BulkMerge"
permalink: /operation/bulkmerge
tags: [repodb, tutorial, bulkmerge, orm, hybrid-orm, sqlserver]
parent: OPERATIONS
---

# BulkMerge

---

This method merges all rows from the client application into the database in bulk. It is supported only for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

<img src="../../assets/images/site/bulkmerge.svg" />

## Use Case

Use this method to merge rows at high speed. It leverages the native bulk operation from ADO.NET via the [SqlBulkCopy](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqlbulkcopy?view=dotnet-plat-ext-7.0) class.

For merging 1,000 or more rows, prefer this method over [MergeAll](/operation/mergeall).

## Special Arguments

The `qualifiers`, `isReturnIdentity`, and `usePhysicalPseudoTempTable` arguments are available for this operation.

`qualifiers` defines the qualifier fields used in the operation, corresponding to the WHERE clause. Defaults to the primary column if not specified.

`isReturnIdentity` controls whether newly generated identity values are set back on the data entities. Disabled by default.

`usePhysicalPseudoTempTable` controls whether a physical pseudo-table is created during the operation. Defaults to a temporary table (e.g., `#TableName`).

{: .important }
> Do not enable `usePhysicalPseudoTempTable` when using parallelism. Always use the session-based non-physical pseudo-temporary table in parallel scenarios.

## Identity Setting Alignment

When `isReturnIdentity` is enabled, the library adds a column named `__RepoDb_OrderColumn` to the pseudo-temporary table whenever an identity field is present in the target table. This column holds the index of each entity model from the [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object.

During the bulk operation, a [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) is created targeting this column with the entity's index value, ensuring correct order alignment. The pseudo-temporary table result set is ordered by this column before the actual merge into the target table.

When assigning identity values back to the data models, the `__RepoDb_OrderColumn` value is used to locate the correct entity in the [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object, and the compiled identity-setter function assigns the value to the identity property.

## Caveats

RepoDB automatically sets `options` to `SqlBulkCopyOptions.KeepIdentity` when no qualifiers are provided and the table has an IDENTITY primary key column. The same applies when there is no primary key but an IDENTITY column is defined.

This operation creates a pseudo-temporary table internally. The database user must have permission to create tables, or a [SqlException](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqlexception?view=dotnet-plat-ext-6.0) will be thrown.

## Usability

Given a list of `Person` models containing both existing and new rows, the following example bulk-merges them into the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var mergedRows = connection.BulkMerge(people);
}
```

To specify a batch size:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var mergedRows = connection.BulkMerge(people, batchSize: 100);
}
```

{: .note }
> By default, the batch size is 10, equal to the `Constant.DefaultBatchOperationSize` value.

#### DataTable

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var mergedRows = connection.BulkMerge<Person>(table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new SqlConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new SqlConnection(destinationConnectionString))
    {
        var mergedRows = destinationConnection.BulkMerge("Person", result,
            qualifiers: Field.From("LastName", "DateOfBirth"));
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new SqlConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM [dbo].[Person] WHERE (IsActive = 1);"))
    {
        using (var destinationConnection = new SqlConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkMerge<Person>(reader);
        }
    }
}
```

To bulk-merge via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var mergedRows = connection.BulkMerge<Person>(reader);
    }
}
```

## Field Qualifiers

By default, the primary column is used as the qualifier. To override, pass a list of [Field](/class/field) objects in the `qualifiers` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge<Person>(people,
        qualifiers: new { e.LastName, e.DateOfBirth });
}
```

{: .important }
> Use indexed columns from the target table as qualifiers to maximize performance.

## Column Mappings

Add column mappings using the `BulkInsertMapItem` class.

```csharp
var mappings = new List<BulkInsertMapItem>();

// Add the mappings
mappings.Add(new BulkInsertMapItem("SourceId", "DestinationId"));
mappings.Add(new BulkInsertMapItem("SourceName", "DestinationName"));
mappings.Add(new BulkInsertMapItem("SourceIsActive", "DestinationIsActive"));
mappings.Add(new BulkInsertMapItem("SourceDateInsertedUtc", "DestinationDateInsertedUtc"));

// Execute
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people,
        mappings: mappings);
}
```

## Bulk Copy Options

Pass a `SqlBulkCopyOptions` value via the `options` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people,
        options: SqlBulkCopyOptions.KeepIdentity);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge("[dbo].[Person]", people);
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people,
        hints: "WITH (TABLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people,
        hints: SqlServerTableHints.TabLock);
}
```
