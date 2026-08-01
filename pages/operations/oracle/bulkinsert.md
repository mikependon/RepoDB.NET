---
layout: default
sidebar: operations
title: "BulkInsert"
permalink: /operation/oracle/bulkinsert
tags: [repodb, tutorial, bulkinsert, orm, hybrid-orm, oracle]
parent: "Oracle"
grand_parent: OPERATIONS
---

# BulkInsert

---

This method inserts all rows from the client application into the database in bulk. It is supported for [Oracle](https://www.nuget.org/packages/RepoDb.Oracle.BulkOperations).

{: .note }
> This page documents the Oracle-specific arguments and examples. For the SQL Server implementation, see [BulkInsert (SQL Server)](/operation/sqlserver/bulkinsert).

## Use Case

Use this method to insert rows at high speed. It leverages the native bulk operation from ODP.NET via the [OracleBulkCopy](https://docs.oracle.com/en/database/oracle/oracle-data-access-components/23.9/odpnt/OracleBulkCopyClass.html) class, which always performs a direct-path load.

For inserting 1,000 or more rows, prefer this method over [InsertAll](/operation/insertall).

Rows are written straight to the target table. A staging table is only used when `identityBehavior` is set to `ReturnIdentity` (see below) — see [Operations (Oracle)](/operation/oracle) for the underlying mechanics.

## Special Arguments

The `mappings`, `bulkCopyTimeout`, `batchSize`, `identityBehavior` and `pseudoTableType` arguments are available for this operation.

`mappings` (via `OracleBulkInsertMapItem`) defines explicit column mappings between the source properties and the destination columns. When omitted, columns are auto-mapped by name (case-insensitive).

`bulkCopyTimeout` overrides the command timeout, in seconds.

`batchSize` overrides the number of rows sent to the server per batch. When not set, all items are sent at once.

`identityBehavior` (via [OracleBulkImportIdentityBehavior](/enumeration/oracle/oraclebulkimportidentitybehavior)) controls whether newly generated identity values are set back on the data entities. Disabled by default. Since Oracle does not support `RETURNING ... BULK COLLECT INTO` on an `INSERT ... SELECT` statement, enabling this routes the operation through a staging table instead, pre-generating identity values via the backing sequence's `NEXTVAL`.

`pseudoTableType` (via [OracleBulkImportPseudoTableType](/enumeration/oracle/oraclebulkimportpseudotabletype)) controls the kind of staging table used when `identityBehavior` is `ReturnIdentity`.

{: .important }
> Every `pseudoTableType` value currently resolves to `Physical` at runtime — see [Operations (Oracle)](/operation/oracle#pseudo-table-type) for details.

## Identity Setting Alignment

When `identityBehavior` is `ReturnIdentity`, the library adds a `__RepoDb_OrderColumn` column to the staging table to track each entity's position in the source [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0). This ensures the identity values returned from the database are assigned back to the correct entity via the compiled identity-setter function.

## Usability

The following example defines a method that produces a list of `Person` objects, then bulk-inserts 10,000 rows into the `Person` table.

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
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people);
}
```

To specify a batch size:

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people, batchSize: 100);
}
```

{: .note }
> When `batchSize` is not set, all rows are sent to the server in a single batch.

To return the newly generated identity values:

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: OracleBulkImportIdentityBehavior.ReturnIdentity);
}
```

#### DataTable

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var table = ConvertToDataTable(people);
    var insertedRows = connection.BulkInsert("Person", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new OracleConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new OracleConnection(destinationConnectionString))
    {
        var insertedRows = destinationConnection.BulkInsert("Person", result);
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new OracleConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM Person"))
    {
        using (var destinationConnection = new OracleConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkInsert("Person", reader);
        }
    }
}
```

To bulk-insert via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var insertedRows = connection.BulkInsert("Person", reader);
    }
}
```

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
    var insertedRows = connection.BulkInsert(people,
        mappings: mappings);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert("Person", people);
}
```

## Async Method

An equivalent [BulkInsertAsync](/operation/oracle/bulkinsert) method is also available.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = await connection.BulkInsertAsync(people);
}
```
