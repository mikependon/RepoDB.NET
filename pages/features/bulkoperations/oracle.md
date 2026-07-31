---
layout: default
sidebar: features
title: "Oracle"
description: "It is the process of bringing all the data from the application into the database server at once, and at the same time, ignoring some database specific activities behind the scene. Thus gives you maximum performance during the operation."
permalink: /feature/bulkoperations/oracle
tags: [repodb, bulk, bulk-operations, oracle]
parent: "Bulk Operations"
grand_parent: FEATURES
---

# Bulk Operations (Oracle)

---

For Oracle, the underlying implementation is leveraging the [OracleBulkCopy](https://docs.oracle.com/en/database/oracle/oracle-data-access-components/23.9/odpnt/OracleBulkCopyClass.html) class of the [Oracle.ManagedDataAccess.Core](https://www.nuget.org/packages/Oracle.ManagedDataAccess.Core) namespace, which always performs a direct-path load.

For [BulkInsert](/operation/bulkinsert), the entities/rows are written straight to the target table (or, when [OracleBulkImportIdentityBehavior.ReturnIdentity](/enumeration/oracle/oraclebulkimportidentitybehavior) is requested, identities are read back via a `RETURNING ... INTO` array bind).

For [BulkDelete](/operation/bulkdelete), [BulkDeleteByKey](/operation/bulkdeletebykey), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate), a pseudo (staging) table is created under a transaction context. The library writes to it via [BulkInsert](/operation/bulkinsert) internally, then cascades the changes to the original table using the correct SQL statement.

{: .note }
> The data is brought together from the client application into the database server (at one-go). It then gets processed together at the same time.

The other bulk operations can be optimized further by targeting the underlying table indexes (via qualifiers). Pass a list of [Field](/class/field) objects when calling the operations.

## Pseudo Table Type

The [OracleBulkImportPseudoTableType](/enumeration/oracle/oraclebulkimportpseudotabletype) enum lets you choose between a Global Temporary Table (session-isolated) and an ordinary heap table for the staging table.

{: .important }
> Every value currently resolves to `Physical` at runtime. ODP.NET's direct-path load cannot write into a Global Temporary Table (it fails with `ORA-39826`), so the `Memory` option is not usable yet. Because a physical pseudo-table has no per-session isolation, avoid running concurrent bulk operations against the same target table until this is resolved.

## Supported Objects

Below are the following objects supported by the bulk operations.

- System.DataTable
- System.Data.Common.DbDataReader
- IEnumerable&lt;T&gt;
- ExpandoObject
- IDictionary&lt;string, object&gt;

## Special Arguments

The arguments below are available on most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the fields used to match existing rows, corresponding to the `WHERE` clause. Defaults to the primary key when not provided. |
| `identityBehavior` | Via [OracleBulkImportIdentityBehavior](/enumeration/oracle/oraclebulkimportidentitybehavior), controls whether the identity property is kept as-is, or whether the newly generated identity values are returned back to the entities after [BulkInsert](/operation/bulkinsert) or [BulkMerge](/operation/bulkmerge). |
| `pseudoTableType` | Via [OracleBulkImportPseudoTableType](/enumeration/oracle/oraclebulkimportpseudotabletype), controls the kind of staging table created — see [Pseudo Table Type](#pseudo-table-type) above. |
| `batchSize` | Overrides the number of rows sent to the server per batch. When not set, all items are sent at once. |

## Identity Setting Alignment

When `identityBehavior` is set to `ReturnIdentity`, the library adds a `__RepoDb_OrderColumn` column to the pseudo-table to track each entity's position in the source [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0). This ensures the identity values returned from the database are assigned back to the correct entity, regardless of ordering during the underlying `MERGE`.

## Async Methods

All the provided synchronous operations have an equivalent asynchronous (`Async`) counterpart.

---

## BulkDelete

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = connection.BulkDelete<Person>(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## BulkDeleteByKey

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var primaryKeys = new object[] { 10045, 10046, 10047 };
    var deletedRows = connection.BulkDeleteByKey("Person", primaryKeys);
}
```

## BulkInsert

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people);
}
```

To return the newly generated identity values:

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: OracleBulkImportIdentityBehavior.ReturnIdentity);
}
```

## BulkMerge

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var mergedRows = connection.BulkMerge(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## BulkUpdate

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```
