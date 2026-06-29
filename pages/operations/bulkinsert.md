---
layout: default
sidebar: operations
title: "BulkInsert"
permalink: /operation/bulkinsert
tags: [repodb, tutorial, bulkinsert, orm, hybrid-orm, sqlserver]
parent: OPERATIONS
---

# BulkInsert

---

This method inserts all rows from the client application into the database in bulk. It is supported only for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

<img src="../../assets/images/site/bulkinsert.svg" />

## Use Case

Use this method to insert rows at high speed. It leverages the native bulk operation from ADO.NET via the [SqlBulkCopy](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqlbulkcopy?view=dotnet-plat-ext-7.0) class.

For inserting 1,000 or more rows, prefer this method over [InsertAll](/operation/insertall).

## Special Arguments

The `isReturnIdentity` and `usePhysicalPseudoTempTable` arguments are available for this operation.

`isReturnIdentity` controls whether newly generated identity values are set back on the data entities. Disabled by default.

`usePhysicalPseudoTempTable` controls whether a physical pseudo-table is created during the operation. Applies only when `isReturnIdentity` is `true`. Defaults to a temporary table (e.g., `#TableName`).

{: .important }
> Do not enable `usePhysicalPseudoTempTable` when using parallelism. Always use the session-based non-physical pseudo-temporary table in parallel scenarios.

## Identity Setting Alignment

When `isReturnIdentity` is enabled, the library adds a column named `__RepoDb_OrderColumn` to the pseudo-temporary table whenever an identity field is present in the target table. This column holds the index of each entity model from the [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object.

During the bulk operation, a [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) is created targeting this column with the entity's index value, ensuring correct order alignment. The pseudo-temporary table result set is ordered by this column before insertion into the target table.

When assigning identity values back to the data models, the `__RepoDb_OrderColumn` value is used to locate the correct entity in the [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object, and the compiled identity-setter function assigns the value to the identity property.

## Usability

The following example defines a method that produces a list of `Person` objects, then bulk-inserts 10,000 rows into the `[dbo].[Person]` table.

```csharp
private IEnumerable<Person> GetPeople(int count = 1000)
{
    for (var i = 0; i < count; i++)
    {
        yield return new Person
        {
            Name = $"Person-{i}",
            SSN = Guid.NewGuid().ToString(),
            IsActive = true,
            DateInsertedUtc = DateTime.UtcNow
        };
    }
}
```

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people);
}
```

To specify a batch size:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people, batchSize: 100);
}
```

{: .note }
> By default, the batch size is 10, equal to the `Constant.DefaultBatchOperationSize` value.

#### DataTable

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var table = ConvertToDataTable(people);
    var insertedRows = connection.BulkInsert<Person>(table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new SqlConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new SqlConnection(destinationConnectionString))
    {
        var insertedRows = destinationConnection.BulkInsert("Person", result);
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new SqlConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        using (var destinationConnection = new SqlConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkInsert<Person>(reader);
        }
    }
}
```

To bulk-insert via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var insertedRows = connection.BulkInsert<Person>(reader);
    }
}
```

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
    var insertedRows = connection.BulkInsert(people,
        mappings: mappings);
}
```

## Bulk Copy Options

Pass a `SqlBulkCopyOptions` value via the `options` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people,
        options: SqlBulkCopyOptions.KeepIdentity);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert("[dbo].[Person]", people);
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people,
        hints: "WITH (TABLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people,
        hints: SqlServerTableHints.TabLock);
}
```
