---
layout: default
title: "ClickHouse"
has_children: true
permalink: /operation/clickhouse
parent: OPERATIONS
---

# Operations (ClickHouse)

---

For ClickHouse via [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations), every row-load goes through this package's internal `ClickHouseBulkCopy` class — a thin adapter over [ClickHouse.Driver](https://www.nuget.org/packages/ClickHouse.Driver)'s own native `Copy.ClickHouseBulkCopy`, reshaped to the `ColumnMappings`-based API the rest of the library expects.

{: .important }
> You must call `GlobalConfiguration.Setup().UseClickHouse()` once at application startup. See [Get Started for ClickHouse](/tutorial/get-started-clickhouse) for more options.

For [BulkInsert](/operation/clickhouse/bulkinsert), rows are written straight to the target table — there is no pseudo/staging table involved, since the only reason one would be needed (`identityBehavior: ReturnIdentity`) is never supported for ClickHouse.

For [BulkMerge](/operation/clickhouse/bulkmerge), [BulkUpdate](/operation/clickhouse/bulkupdate), [BulkDelete](/operation/clickhouse/bulkdelete) and [BulkDeleteByKey](/operation/clickhouse/bulkdeletebykey), a pseudo (staging) table is created for each call. The library writes to it via its internal `ClickHouseBulkCopy`, then cascades the changes to the original table using the correct SQL statement, then drops the pseudo table.

{: .note }
> The data is brought together from the client application into the database server (at one-go). It then gets processed together at the same time.

## No Identity Support

{: .important }
> ClickHouse has no session-wide scope identity, sequence, or auto-increment mechanism. [ClickHouseBulkImportIdentityBehavior.ReturnIdentity](/enumeration/clickhouse/clickhousebulkimportidentitybehavior) always throws `NotSupportedException` on [BulkInsert](/operation/clickhouse/bulkinsert) and [BulkMerge](/operation/clickhouse/bulkmerge) — the default `KeepIdentity` (writing the value exactly as supplied on the entity) is the only supported behavior. Assign key values on the entity yourself before calling any bulk operation.

## No Reliable Affected-Row Count

{: .important }
> `ALTER TABLE ... UPDATE`/`DELETE` are asynchronous *mutations* in ClickHouse — applied later by background merges, not immediately — and neither they nor a plain `INSERT` report a reliable "rows affected" figure through `ClickHouse.Driver`. Because of this, [BulkMerge](/operation/clickhouse/bulkmerge), [BulkUpdate](/operation/clickhouse/bulkupdate), [BulkDelete](/operation/clickhouse/bulkdelete) and [BulkDeleteByKey](/operation/clickhouse/bulkdeletebykey) all report the number of rows **staged** into the pseudo table, not a confirmed post-mutation count. [BulkInsert](/operation/clickhouse/bulkinsert) reports the number of rows submitted to the driver's bulk copy.

## Pseudo Table Type

The `ClickHouseBulkImportPseudoTableType` enum lets you choose between a `Memory`-engine table (session-isolated) and an ordinary `MergeTree` table for the staging table.

{: .important }
> **Every value currently resolves to `Physical` at runtime**, including an explicit `Memory` and `Auto`'s row-count threshold (5,000 rows). The `Memory`-engine branch is fully implemented in the SQL builder, but the resolution step that picks between them currently maps every input to `Physical` unconditionally, until that path is enabled and verified against a live server. Because a physical pseudo table has no per-session isolation, avoid running concurrent bulk operations against the same target table until this is resolved.

## Supported Objects

Below are the following objects supported by the bulk operations.

- System.DataTable
- System.Data.Common.DbDataReader
- IEnumerable&lt;T&gt;

## The Staging Table Lifecycle

`BulkMerge`, `BulkUpdate`, `BulkDelete` and `BulkDeleteByKey` stage rows into a per-call pseudo table before running a mutation against it. Every call issues a `DROP TABLE IF EXISTS` followed by `CREATE TABLE pseudo ENGINE = MergeTree ORDER BY tuple() AS SELECT ... FROM table WHERE (1 = 0)` (or `ENGINE = Memory` when that branch is reached) to (re)create the pseudo table, shaped after the real table's columns — ClickHouse has no `CREATE TABLE IF NOT EXISTS ... AS SELECT` "replace" form, so the explicit drop always comes first. The pseudo table is dropped again once the operation finishes.

## Operation SQL Statements

Once all the data is in the pseudo (staging) table, the correct SQL statement is used to cascade the changes towards the original table.

{: .note }
> [BulkInsert](/operation/clickhouse/bulkinsert) writes directly into the target table and skips the staging table entirely.

#### For BulkDelete / BulkDeleteByKey

```csharp
> ALTER TABLE OriginalTable DELETE
> WHERE (QualifierField1, QualifierField2) IN (SELECT QualifierField1, QualifierField2 FROM PseudoTempTable);
```

#### For BulkMerge

```csharp
> ALTER TABLE OriginalTable UPDATE
>     Field3 = (SELECT S.Field3 FROM PseudoTempTable S WHERE S.QualifierField1 = OriginalTable.QualifierField1 LIMIT 1),
>     Field4 = (SELECT S.Field4 FROM PseudoTempTable S WHERE S.QualifierField1 = OriginalTable.QualifierField1 LIMIT 1)
> WHERE (QualifierField1) IN (SELECT QualifierField1 FROM PseudoTempTable);
>
> INSERT INTO OriginalTable (Field1, Field2, Field3, Field4)
> SELECT S.Field1, S.Field2, S.Field3, S.Field4 FROM PseudoTempTable S
> LEFT JOIN OriginalTable T ON (T.QualifierField1 = S.QualifierField1)
> WHERE T.QualifierField1 IS NULL;
```

{: .note }
> ClickHouse has no native `MERGE` statement and mutations have no join alias, so `BulkMerge` always performs the same two-statement translation: an `ALTER TABLE ... UPDATE` against the rows that match on `qualifiers` (each updated column pulled via a correlated scalar subquery), followed by an `INSERT ... SELECT` guarded by a `LEFT JOIN ... WHERE ... IS NULL` anti-join for the rows that don't. The anti-join assumes the first qualifier column is never legitimately `NULL` on a real, matched row.

#### For BulkUpdate

```csharp
> ALTER TABLE OriginalTable UPDATE
>     Field3 = (SELECT S.Field3 FROM PseudoTempTable S WHERE S.QualifierField1 = OriginalTable.QualifierField1 LIMIT 1),
>     Field4 = (SELECT S.Field4 FROM PseudoTempTable S WHERE S.QualifierField1 = OriginalTable.QualifierField1 LIMIT 1)
> WHERE (QualifierField1) IN (SELECT QualifierField1 FROM PseudoTempTable);
```

{: .note }
> Unlike [BulkMerge](/operation/clickhouse/bulkmerge), there is no anti-join `INSERT` branch — staged rows with no matching target row are left as-is, not inserted. If every staged column is also a qualifier (i.e. there is nothing left to update), the operation short-circuits and returns `0` without touching the database.

## Special Arguments

The arguments below are available on most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the fields used to match existing rows, corresponding to the `WHERE` clause of the mutation. Defaults to the primary key when not provided. |
| `identityBehavior` | Via [ClickHouseBulkImportIdentityBehavior](/enumeration/clickhouse/clickhousebulkimportidentitybehavior) (`KeepIdentity`, `ReturnIdentity`). Only available on [BulkInsert](/operation/clickhouse/bulkinsert) and [BulkMerge](/operation/clickhouse/bulkmerge). `ReturnIdentity` always throws `NotSupportedException`. |
| `pseudoTableType` | Via [ClickHouseBulkImportPseudoTableType](/enumeration/clickhouse/clickhousebulkimportpseudotabletype) (`Auto`, `Memory`, `Physical`), controls the kind of staging table created — see [Pseudo Table Type](#pseudo-table-type) above. Not applicable to `BulkInsert`. |
| `batchSize` | Overrides the number of rows sent to the server per batch. When not set, the driver's own default (100,000) is used. |
| `mappings` | Via `ClickHouseBulkInsertMapItem`, defines explicit column mappings between the source properties/columns and the destination columns, with an optional explicit ClickHouse type name override. When omitted, columns are auto-mapped by name. Available on [BulkInsert](/operation/clickhouse/bulkinsert), [BulkMerge](/operation/clickhouse/bulkmerge) and [BulkUpdate](/operation/clickhouse/bulkupdate) only. |
| `bulkCopyTimeout` | Accepted for API parity with other providers, but the underlying driver has no per-copy timeout knob, so it currently has no effect. |

## Async Methods

All the provided synchronous operations have an equivalent asynchronous (`Async`) counterpart.

---

## BulkDelete

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = connection.BulkDelete<Person>(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## BulkDeleteByKey

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var primaryKeys = new object[] { 10045, 10046, 10047 };
    var deletedRows = connection.BulkDeleteByKey("Person", primaryKeys);
}
```

## BulkInsert

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people);
}
```

## BulkMerge

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var mergedRows = connection.BulkMerge(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## BulkUpdate

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```
