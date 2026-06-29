---
layout: default
sidebar: operations
title: "BulkUpdate"
permalink: /operation/bulkupdate
tags: [repodb, tutorial, bulkupdate, orm, hybrid-orm, sqlserver]
parent: OPERATIONS
---

# BulkUpdate

---

This method updates all rows from the client application in the database in bulk. It is supported only for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

<img src="../../assets/images/site/bulkupdate.svg" />

## Use Case

Use this method to update rows at high speed. It leverages the native bulk operation from ADO.NET via the [SqlBulkCopy](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqlbulkcopy?view=dotnet-plat-ext-7.0) class.

For updating 1,000 or more rows, prefer this method over [UpdateAll](/operation/updateall).

## Special Arguments

The `qualifiers` and `usePhysicalPseudoTempTable` arguments are available for this operation.

`qualifiers` defines the qualifier fields used in the operation, corresponding to the WHERE clause. Defaults to the primary column if not specified.

`usePhysicalPseudoTempTable` controls whether a physical pseudo-table is created during the operation. Defaults to a temporary table (e.g., `#TableName`).

{: .important }
> Do not enable `usePhysicalPseudoTempTable` when using parallelism. Always use the session-based non-physical pseudo-temporary table in parallel scenarios.

## Caveats

RepoDB automatically sets `options` to `SqlBulkCopyOptions.KeepIdentity` when no qualifiers are provided and the table has an IDENTITY primary key column. The same applies when there is no primary key but an IDENTITY column is defined.

This operation creates a pseudo-temporary table internally. The database user must have permission to create tables, or a [SqlException](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqlexception?view=dotnet-plat-ext-6.0) will be thrown.

## Usability

Given a list of `Person` models, the following example bulk-updates rows in the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people);
}
```

To specify a batch size:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people, batchSize: 100);
}
```

{: .note }
> By default, the batch size is 10, equal to the `Constant.DefaultBatchOperationSize` value.

#### DataTable

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var updatedRows = connection.BulkUpdate<Person>(table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new SqlConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new SqlConnection(destinationConnectionString))
    {
        var updatedRows = destinationConnection.BulkUpdate("Person", result,
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
            var rows = destinationConnection.BulkUpdate<Person>(reader);
        }
    }
}
```

To bulk-update via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var updatedRows = connection.BulkUpdate<Person>(reader);
    }
}
```

## Field Qualifiers

By default, the primary column is used as the qualifier. To override, pass a list of [Field](/class/field) objects in the `qualifiers` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate<Person>(people,
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
    var updatedRows = connection.BulkUpdate(people,
        mappings: mappings);
}
```

## Bulk Copy Options

Pass a `SqlBulkCopyOptions` value via the `options` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people,
        options: SqlBulkCopyOptions.KeepIdentity);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate("[dbo].[Person]", people);
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people,
        hints: "WITH (TABLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people,
        hints: SqlServerTableHints.TabLock);
}
```
