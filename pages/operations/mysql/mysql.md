---
layout: default
title: "MySQL"
nav_order: 6
has_children: true
permalink: /operation/mysql
parent: OPERATIONS
---

# Operations (MySQL)

---

For MySQL via the [MySql.Data](https://www.nuget.org/packages/MySql.Data) driver ([RepoDb.MySql.BulkOperations](https://www.nuget.org/packages/RepoDb.MySql.BulkOperations)), every row-load goes through this package's own internal `MySqlBulkCopy` class — a `LOAD DATA LOCAL INFILE`-based stand-in built on top of `MySql.Data`'s `MySqlBulkLoader`, since `MySql.Data` ships no genuine streaming bulk-copy API of its own (unlike `SqlBulkCopy`, it has no reader-streaming `WriteToServer(IDataReader)` overload). This package's `MySqlBulkCopy` first serializes whatever rows it's given (entities, a `DataTable`, or a reader) to a temporary tab-delimited file, hands that file to `MySqlBulkLoader`, then deletes it once the load completes.

{: .note }
> This is a separate implementation from [Operations (MySqlConnector)](/operation/mysqlconnector), which uses the third-party [MySqlConnector](https://www.nuget.org/packages/MySqlConnector) package's own `MySqlBulkCopy` type. The two packages are unrelated and cannot be mixed.

{: .important }
> The connection string needs `AllowLoadLocalInfile=True;AllowUserVariables=True;` — the former lets the client send `LOAD DATA LOCAL INFILE`, which this package's internal `MySqlBulkCopy` uses for every row-load, and the latter lets the staging-table SQL use session user variables and `PREPARE`/`EXECUTE` for its identity pre-assignment and nullability-toggling steps. The server also needs its `local_infile` global variable turned on (`SET GLOBAL local_infile = 1;`, requires `SUPER`/`SYSTEM_VARIABLES_ADMIN`) — it's off by default. You must also call `RepoDb.MySqlBootstrap.Initialize()` once at application startup. See [Installation](/tutorial/installation) for more options.

For [BulkInsert](/operation/mysql/bulkinsert), the entities/rows are written straight to the target table — unless `identityBehavior: MySqlBulkImportIdentityBehavior.ReturnIdentity` is requested, in which case a staging (pseudo) table is used instead, so the newly generated `AUTO_INCREMENT` values can be pre-assigned and read back before the rows are copied into the target table.

For [BulkDelete](/operation/mysql/bulkdelete), [BulkDeleteByKey](/operation/mysql/bulkdeletebykey), [BulkMerge](/operation/mysql/bulkmerge) and [BulkUpdate](/operation/mysql/bulkupdate), a pseudo (staging) table is created for each call. The library writes to it via its internal `MySqlBulkCopy`, then cascades the changes to the original table using the correct SQL statement, then drops the pseudo table.

{: .note }
> The data is brought together from the client application into the database server (at one-go). It then gets processed together at the same time.

The other bulk operations can be optimized further by targeting the underlying table indexes (via qualifiers). Pass a list of [Field](/class/field) objects when calling the operations.

## Pseudo Table Type

The `MySqlBulkImportPseudoTableType` enum lets you choose between a `TEMPORARY` table (session-isolated) and an ordinary heap table for the staging table.

{: .important }
> **Every value currently resolves to `Physical` at runtime**, including an explicit `Memory` and `Auto`'s row-count threshold. The `TEMPORARY TABLE` branch is fully implemented in the SQL builder, but the resolution step that picks between them (`ResolvePseudoTableType`) currently maps every input to `Physical` unconditionally, until that path is enabled and verified against a live server. Because a physical pseudo-table has no per-session isolation, avoid running concurrent bulk operations against the same target table until this is resolved.

## Supported Objects

Below are the following objects supported by the bulk operations.

- System.DataTable
- System.Data.Common.DbDataReader
- IEnumerable&lt;T&gt;
- ExpandoObject
- IDictionary&lt;string, object&gt;

## The Staging Table Lifecycle

`BulkMerge`, `BulkUpdate`, `BulkDelete`, `BulkDeleteByKey`, and `BulkInsert` with `ReturnIdentity` stage rows into a per-call pseudo table before running a set-based statement against it. Every call — not just the first one for a given table — issues a fresh `DROP TABLE IF EXISTS` followed by `CREATE TABLE ... AS SELECT ... WHERE (1 = 0)` (or `CREATE TEMPORARY TABLE ...` for `Memory`) to (re)create the pseudo table, shaped after the real table's columns, plus one extra surrogate column — `__RepoDbBulkRowOrder__ BIGINT AUTO_INCREMENT PRIMARY KEY` — that gives the staged rows a deterministic order to read back in. The pseudo table is dropped again once the operation finishes.

{: .important }
> Because `CREATE TABLE`/`DROP TABLE` are DDL, and DDL causes an **implicit COMMIT** in MySQL, **every** `BulkMerge`/`BulkUpdate`/`BulkDelete`/`BulkDeleteByKey`/`BulkInsert`-with-`ReturnIdentity` call implicitly commits any other uncommitted work already pending on that connection — both when the pseudo table is (re)created at the start of the call and again when it's dropped at the end. Keep this in mind if you're bulk-writing inside a larger transaction alongside other statements.

## Operation SQL Statements

Once all the data is in the pseudo (staging) table, the correct SQL statement is used to cascade the changes towards the original table.

{: .note }
> [BulkInsert](/operation/mysql/bulkinsert) writes directly into the target table and skips the staging table entirely — unless `identityBehavior` is set to `ReturnIdentity`, in which case a staging table is used to pre-assign identity values first (see [Identity Setting Alignment](#identity-setting-alignment) below).

#### For BulkDelete / BulkDeleteByKey

```csharp
> DELETE T FROM `OriginalTable` T
> INNER JOIN `PseudoTempTable` S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2);
```

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
> MySQL has no native `MERGE` statement, so `BulkMerge` always performs the same two-statement translation: an `UPDATE ... INNER JOIN` against the rows that match on `qualifiers`, followed by an `INSERT ... SELECT` guarded by a `LEFT JOIN ... WHERE ... IS NULL` anti-join for the rows that don't. The identity column, if any, is always left out of the `INSERT` column list — a brand-new row's identity property is typically an unset default (e.g. `0`), not a real value meant to be inserted as-is.

#### For BulkUpdate

```csharp
> UPDATE `OriginalTable` T INNER JOIN `PseudoTempTable` S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> SET T.Field3 = S.Field3, T.Field4 = S.Field4;
```

{: .note }
> Unlike [BulkMerge](/operation/mysql/bulkmerge), there is no anti-join `INSERT` branch — staged rows with no matching target row are left as-is, not inserted.

## Special Arguments

The arguments below are available on most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the fields used to match existing rows, corresponding to the `WHERE`/`ON` clause. Defaults to the primary key when not provided. |
| `identityBehavior` | Via `MySqlBulkImportIdentityBehavior` (`KeepIdentity`, `ReturnIdentity`), controls whether the identity column is left for `AUTO_INCREMENT` to generate, or whether the newly generated (or matched) identity values are returned back to the entities after [BulkInsert](/operation/mysql/bulkinsert) or [BulkMerge](/operation/mysql/bulkmerge). |
| `pseudoTableType` | Via `MySqlBulkImportPseudoTableType` (`Auto`, `Memory`, `Physical`), controls the kind of staging table created — see [Pseudo Table Type](#pseudo-table-type) above. |
| `batchSize` | Overrides the number of rows sent to the server per batch. When not set, all items are sent at once. |
| `mappings` | Via `MySqlBulkInsertMapItem`, defines explicit column mappings between the source properties/columns and the destination columns. When omitted, columns are auto-mapped by name. Each mapping can optionally carry a `MySqlDbType` override, but it currently has no effect — see [Column Mappings](/operation/mysql/bulkinsert#column-mappings). |
| `bulkCopyTimeout` | Overrides the command timeout, in seconds. |

## Identity Setting Alignment

When `identityBehavior` is set to `ReturnIdentity`, the library adds a surrogate `__RepoDbBulkRowOrder__` column to the pseudo table to track each entity's/row's position in the source [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) — MySQL has no equivalent to Oracle's implicit `ROWID`, so this stands in for it. Identity values are pre-assigned into the pseudo table via a session user variable seeded from the target table's current `MAX(identity) + 1`, then copied over as literal values, since MySQL's `AUTO_INCREMENT` has no per-row `NEXTVAL`-style mechanism to pull values from ahead of the actual `INSERT`. The resultset is then read back ordered by `__RepoDbBulkRowOrder__` so each value is assigned to the correct entity, regardless of ordering during the underlying `INSERT`.

{: .important }
> Returning identity values relies on session user variables (and, for `BulkInsert`/`BulkMerge`'s nullable-column step, a dynamically prepared `ALTER TABLE` statement) — this is why `AllowUserVariables=True` is required on the connection string.

## The Transaction Boundary

This package's internal `MySqlBulkCopy` is constructed from a bare `MySqlConnection` and never receives a `MySqlTransaction`, and it issues `LOAD DATA LOCAL INFILE` directly against that connection rather than through a command enlisted in your transaction.

{: .important }
> Whether a rolled-back transaction leaves already-loaded rows behind has not been verified against a live server — treat it as unconfirmed until you've checked the behavior for your MySQL version and storage engine. See the [release notes](/release/mysqlbulk) for the current verification status of this package.

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
using (var connection = new MySqlConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = connection.BulkDelete<Person>(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## BulkDeleteByKey

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var primaryKeys = new object[] { 10045, 10046, 10047 };
    var deletedRows = connection.BulkDeleteByKey("Person", primaryKeys);
}
```

## BulkInsert

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people);
}
```

To return the newly generated identity values:

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: MySqlBulkImportIdentityBehavior.ReturnIdentity);
}
```

## BulkMerge

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var mergedRows = connection.BulkMerge(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## BulkUpdate

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```
