---
layout: default
title: "MariaDb"
has_children: true
permalink: /operation/mariadb
parent: OPERATIONS
---

# Operations (MariaDb)

---

For MariaDB via the [MySql.Data](https://www.nuget.org/packages/MySql.Data)-based [RepoDb.Connector.MariaDb](https://www.nuget.org/packages/RepoDb.Connector.MariaDb) driver ([RepoDb.MariaDb.BulkOperations](https://www.nuget.org/packages/RepoDb.MariaDb.BulkOperations)), every row-load goes through this package's own internal `MariaDbBulkCopy` class — a `LOAD DATA LOCAL INFILE`-based stand-in built on top of `RepoDb.Connector.MariaDb`'s `MariaDbBulkLoader`, since the underlying `MySql.Data` driver ships no genuine streaming bulk-copy API of its own (unlike `SqlBulkCopy`, it has no reader-streaming `WriteToServer(IDataReader)` overload). This package's `MariaDbBulkCopy` first serializes whatever rows it's given (entities, a `DataTable`, or a reader) to a tab-delimited temp file, hands that file to `MariaDbBulkLoader`, then deletes it once the load completes.

{: .note }
> This is a separate implementation from [Operations (MariaDbConnector)](/operation/mariadbconnector), which uses the `MySqlConnector`-based [RepoDb.Connector.MariaDbConnector](https://www.nuget.org/packages/RepoDb.Connector.MariaDbConnector) package's own `MariaDbBulkCopy` type. The two packages are unrelated and cannot be mixed.

{: .important }
> The connection string needs `AllowLoadLocalInfile=True;AllowUserVariables=True;` — the former lets the client send `LOAD DATA LOCAL INFILE`, which this package's internal `MariaDbBulkCopy` uses for every row-load, and the latter lets the staging-table SQL use session user variables and `PREPARE`/`EXECUTE` for its identity pre-assignment and nullability-toggling steps. The server also needs its `local_infile` global variable turned on (`SET GLOBAL local_infile = 1;`, requires `SUPER`) — it's off by default. You must also call `GlobalConfiguration.Setup().UseMariaDb()` (or `MariaDbBootstrap.Initialize()`) once at application startup. See [Get Started for MariaDB](/tutorial/get-started-mariadb) for more options.

For [BulkInsert](/operation/mariadb/bulkinsert), the entities/rows are written straight to the target table — unless `identityBehavior: MariaDbBulkImportIdentityBehavior.ReturnIdentity` is requested, in which case a staging (pseudo) table is used instead, so the newly generated `AUTO_INCREMENT` values can be pre-assigned and read back before the rows are copied into the target table.

For [BulkDelete](/operation/mariadb/bulkdelete), [BulkDeleteByKey](/operation/mariadb/bulkdeletebykey), [BulkMerge](/operation/mariadb/bulkmerge) and [BulkUpdate](/operation/mariadb/bulkupdate), a pseudo (staging) table is created for each call. The library writes to it via its internal `MariaDbBulkCopy`, then cascades the changes to the original table using the correct SQL statement, then drops the pseudo table.

{: .note }
> The data is brought together from the client application into the database server (at one-go). It then gets processed together at the same time.

The other bulk operations can be optimized further by targeting the underlying table indexes (via qualifiers). Pass a list of [Field](/class/field) objects when calling the operations.

## Pseudo Table Type

The `MariaDbBulkImportPseudoTableType` enum lets you choose between a `TEMPORARY` table (session-isolated) and an ordinary heap table for the staging table.

{: .important }
> **Every value currently resolves to `Physical` at runtime**, including an explicit `Memory` and `Auto`'s row-count threshold (5,000 rows). The `TEMPORARY TABLE` branch is fully implemented in the SQL builder, but the resolution step that picks between them currently maps every input to `Physical` unconditionally, until that path is enabled and verified against a live server. Because a physical pseudo-table has no per-session isolation, avoid running concurrent bulk operations against the same target table until this is resolved.

## Supported Objects

Below are the following objects supported by the bulk operations.

- System.DataTable
- System.Data.Common.DbDataReader
- IEnumerable&lt;T&gt;
- ExpandoObject
- IDictionary&lt;string, object&gt;

## The Staging Table Lifecycle

`BulkMerge`, `BulkUpdate`, `BulkDelete`, `BulkDeleteByKey`, and `BulkInsert` with `ReturnIdentity` stage rows into a per-call pseudo table before running a set-based statement against it. Every call issues a fresh `DROP TABLE IF EXISTS` followed by `CREATE TABLE ... AS SELECT ... WHERE (1 = 0)` (or `CREATE TEMPORARY TABLE ...` when the `Memory` branch is reached) to (re)create the pseudo table, shaped after the real table's columns, plus one extra surrogate column — `__RepoDbBulkRowOrder__ BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY` — that gives the staged rows a deterministic order to read back in, standing in for the physical-order guarantee other engines' `ROWID`/ordinal give for free. `CREATE TABLE ... AS SELECT` never carries over a source column's own `PRIMARY KEY` or `AUTO_INCREMENT` attribute, so the pseudo table's identity column (when present) always comes out as a plain, non-generating column. When qualifiers are present, an index is created on the pseudo table's qualifier columns (`CREATE INDEX __RepoDbBulkQualifierIndex__ ...`) before the bulk-load, since `CREATE TABLE ... AS SELECT` also never carries over key/index definitions — without it, every merge/update/delete join would fall back to a full pseudo-table scan. The pseudo table is dropped again once the operation finishes.

## Operation SQL Statements

Once all the data is in the pseudo (staging) table, the correct SQL statement is used to cascade the changes towards the original table.

{: .note }
> [BulkInsert](/operation/mariadb/bulkinsert) writes directly into the target table and skips the staging table entirely — unless `identityBehavior` is set to `ReturnIdentity`, in which case a staging table is used to pre-assign identity values first (see [Identity Setting Alignment](#identity-setting-alignment) below).

#### For BulkDelete / BulkDeleteByKey

```csharp
> DELETE T FROM `OriginalTable` T
> INNER JOIN `PseudoTempTable` S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2);
```

{: .note }
> MariaDB supports deleting directly through a join, so there is no need for a `ROWID`/`EXISTS`-based workaround.

#### For BulkMerge

```csharp
> UPDATE `OriginalTable` T INNER JOIN `PseudoTempTable` S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> SET T.Field3 = S.Field3, T.Field4 = S.Field4;
>
> INSERT INTO `OriginalTable` (Field1, Field2, ...)
> SELECT S.Field1, S.Field2, ... FROM `PseudoTempTable` S
> LEFT JOIN `OriginalTable` T ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> WHERE T.QualifierField1 IS NULL;
```

{: .note }
> MariaDB has no native `MERGE` statement, and `INSERT ... ON DUPLICATE KEY UPDATE` only detects a "duplicate" via an actual unique/primary key constraint — which the caller's `qualifiers` are not guaranteed to correspond to. So `BulkMerge` always performs the same two-statement translation instead: an `UPDATE ... INNER JOIN` against the rows that match on `qualifiers`, followed by an `INSERT ... SELECT` guarded by a `LEFT JOIN ... WHERE ... IS NULL` anti-join for the rows that don't. The identity column, if any, is always left out of the `INSERT` column list — a brand-new row's identity property is typically an unset default (e.g. `0`), not a real value meant to be inserted as-is. The anti-join assumes the first qualifier column is never legitimately `NULL` on a real, matched row.

#### For BulkUpdate

```csharp
> UPDATE `OriginalTable` T INNER JOIN `PseudoTempTable` S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> SET T.Field3 = S.Field3, T.Field4 = S.Field4;
```

{: .note }
> Unlike [BulkMerge](/operation/mariadb/bulkmerge), there is no anti-join `INSERT` branch — staged rows with no matching target row are left as-is, not inserted. If every staged column is also a qualifier (i.e. there is nothing left to update), the operation short-circuits and returns `0` without touching the database.

## Special Arguments

The arguments below are available on most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the fields used to match existing rows, corresponding to the `WHERE`/`ON` clause. Defaults to the primary key when not provided. |
| `identityBehavior` | Via `MariaDbBulkImportIdentityBehavior` (`KeepIdentity`, `ReturnIdentity`), controls whether the identity column is left for `AUTO_INCREMENT` to generate, or whether the newly generated (or matched) identity values are returned back to the entities after [BulkInsert](/operation/mariadb/bulkinsert) or [BulkMerge](/operation/mariadb/bulkmerge). |
| `pseudoTableType` | Via `MariaDbBulkImportPseudoTableType` (`Auto`, `Memory`, `Physical`), controls the kind of staging table created — see [Pseudo Table Type](#pseudo-table-type) above. |
| `batchSize` | Overrides the number of rows sent to the server per batch. When not set, all items are sent at once. |
| `mappings` | Via `MariaDbBulkInsertMapItem`, defines explicit column mappings between the source properties/columns and the destination columns, with an optional explicit `MariaDbType` override. When omitted, columns are auto-mapped by name. |
| `bulkCopyTimeout` | Overrides the command timeout, in seconds. |

## Identity Setting Alignment

When `identityBehavior` is set to `ReturnIdentity`, identity values are pre-assigned into the pseudo table *before* the real `INSERT`/`MERGE`, rather than relying on `LAST_INSERT_ID()` plus positional arithmetic after a multi-row insert (not safe under MariaDB's default interleaved `innodb_autoinc_lock_mode`, which does not guarantee gap-free identity allocation for that statement shape under concurrent writers). The seed value is read live as `MAX(identityColumn) + 1` directly off the target table's row data — deliberately **not** from `information_schema.TABLES.AUTO_INCREMENT`, which MariaDB can cache for up to `information_schema_stats_expiry` seconds (24 hours by default) and only refreshes via `ANALYZE TABLE` or expiry, which could otherwise seed from a stale, pre-insert counter and collide on an already-used primary key. The seed is then incremented once per row via a session user variable (`SET @repodb_seq := ...`) and copied over as literal values into the pseudo table's identity column, so the real table's own `AUTO_INCREMENT` never fires for these rows (MariaDB still auto-advances its internal counter past whatever explicit value it sees, so this creates no future collisions). The pre-assigned identity value and the `__RepoDbBulkRowOrder__` surrogate column are what let the final `SELECT ... ORDER BY` report each row's identity value back in the original entity/row list order.

{: .important }
> The seed lookup and the pre-assignment statement are two separate round trips, leaving a small race window against a concurrent writer to the same table — no table-level locking is used to close it (`LOCK TABLES` would silently commit any transaction already open on the connection). For [BulkMerge](/operation/mariadb/bulkmerge) with `ReturnIdentity`, matched rows keep their existing identity value (copied from the real table onto the pseudo row via an `UPDATE ... INNER JOIN`) before unmatched rows are assigned a fresh one — five statements run in total instead of the two-statement no-identity path. Returning identity values relies on session user variables (and, for the nullable-column step below, a dynamically prepared `ALTER TABLE` statement) — this is why `AllowUserVariables=True` is required on the connection string.

`CREATE TABLE ... AS SELECT` carries a source column's `NOT NULL` attribute over to the pseudo table, but the identity column is deliberately left unpopulated during the bulk-load (it's assigned afterward), so it must be made nullable first. Unlike some engines' "just the nullability" `ALTER` form, MariaDB's `MODIFY COLUMN` requires the column's full type to be re-stated, so the current type is looked up from `information_schema.COLUMNS.COLUMN_TYPE` and used to build the `ALTER TABLE` text dynamically via `PREPARE`/`EXECUTE`/`DEALLOCATE PREPARE`.

## The Transaction Boundary

This package's internal `MariaDbBulkCopy` is constructed from a bare `MariaDbConnection` and never receives a `MariaDbTransaction`, and it issues `LOAD DATA LOCAL INFILE` directly against that connection rather than through a command enlisted in your transaction. The surrounding staging-table DDL and the final cascading statement (`INSERT`/`UPDATE`/`DELETE`) do participate in the caller-supplied transaction, since they run through `connection.ExecuteNonQuery(..., transaction: transaction)`.

{: .important }
> Whether a rolled-back transaction leaves already-loaded rows behind has not been verified against a live server — treat it as unconfirmed until you've checked the behavior for your MariaDB version and storage engine. See the [release notes](/release/mariadbbulk) for the current verification status of this package.

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
using (var connection = new MariaDbConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = connection.BulkDelete<Person>(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## BulkDeleteByKey

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var primaryKeys = new object[] { 10045, 10046, 10047 };
    var deletedRows = connection.BulkDeleteByKey("Person", primaryKeys);
}
```

## BulkInsert

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people);
}
```

To return the newly generated identity values:

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: MariaDbBulkImportIdentityBehavior.ReturnIdentity);
}
```

## BulkMerge

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var mergedRows = connection.BulkMerge(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## BulkUpdate

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```
