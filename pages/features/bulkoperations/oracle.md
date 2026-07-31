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

For [BulkInsert](/operation/bulkinsert), the entities/rows are written straight to the target table (or, when [OracleBulkImportIdentityBehavior.ReturnIdentity](/enumeration/oracle/oraclebulkimportidentitybehavior) is requested, a staging table is used instead to pre-generate identity values via the backing sequence's `NEXTVAL`, since Oracle does not support `RETURNING ... BULK COLLECT INTO` on an `INSERT ... SELECT` statement).

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

## Operation SQL Statements

Once all the data is in the staging (pseudo) table, the correct SQL statement is used to cascade the changes towards the original table.

{: .note }
> [BulkInsert](/operation/bulkinsert) writes directly into the target table and skips the staging table entirely — unless `identityBehavior` is set to `ReturnIdentity`, in which case a staging table is used to pre-generate identity values first (see above).

#### For BulkDelete / BulkDeleteByKey

```csharp
> DELETE FROM "OriginalTable"
> WHERE ROWID IN (
>     SELECT T.ROWID
>     FROM "OriginalTable" T
>     INNER JOIN "PseudoTempTable" S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> );
```

{: .note }
> Oracle's `DELETE` cannot directly target a joined result the way SQL Server's `DELETE ... FROM ... INNER JOIN` can, since the staging table carries no key/index of its own. Matching by `ROWID` sidesteps that restriction.

#### For BulkMerge

```csharp
> MERGE INTO "OriginalTable" T USING "PseudoTempTable" S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> WHEN MATCHED THEN
> UPDATE SET T.Field3 = S.Field3, T.Field4 = S.Field4
> WHEN NOT MATCHED THEN
> INSERT (Field1, Field2, ...) VALUES (S.Field1, S.Field2, ...);
```

{: .note }
> The identity column, if any, is always left out of the `INSERT` column list — a brand-new row's identity property is typically an unset default (e.g. `0`), not a real value meant to be inserted as-is.

#### For BulkUpdate

```csharp
> MERGE INTO "OriginalTable" T USING "PseudoTempTable" S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> WHEN MATCHED THEN
> UPDATE SET T.Field3 = S.Field3, T.Field4 = S.Field4;
```

{: .note }
> Unlike [BulkMerge](/operation/bulkmerge), there is no `WHEN NOT MATCHED` branch — staged rows with no matching target row are left as-is, not inserted.

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

## BatchSize

All the provided operations have a `batchSize` argument that lets you override the number of rows wired-up to the server per batch. By default it is `null`, meaning all items are sent together in one-go.

Use this argument if you wish to optimize the operation based on certain situations.

- Network Latency
- Infrastructure
- No. of Columns
- Type of Data

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
