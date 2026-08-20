---
layout: default
title: "Db2"
has_children: true
permalink: /operation/db2
parent: OPERATIONS
---

# Operations (Db2)

---

For Db2, the underlying implementation is leveraging the [DB2BulkCopy](https://www.ibm.com/docs/en/db2/11.5?topic=classes-db2bulkcopy-class) class of the [IBM Data Server .NET Provider](https://www.nuget.org/packages/Net.IBM.Data.Db2) (`IBM.Data.Db2` namespace).

For [BulkInsert](/operation/db2/bulkinsert), the entities/rows are written straight to the target table (or, when [Db2BulkImportIdentityBehavior.ReturnIdentity](/enumeration/db2/db2bulkimportidentitybehavior) is requested, a staging table is used instead so the generated identity values can be read back via Db2's `SELECT ... FROM FINAL TABLE (INSERT ...)` clause on the very same statement that loads the real table — no separate pre-generation round-trip is needed the way Oracle's `NEXTVAL` approach requires).

For [BulkDelete](/operation/db2/bulkdelete), [BulkDeleteByKey](/operation/db2/bulkdeletebykey), [BulkMerge](/operation/db2/bulkmerge) and [BulkUpdate](/operation/db2/bulkupdate), a pseudo (staging) table is created (and truncated) under a transaction context. The library writes to it via [BulkInsert](/operation/db2/bulkinsert) internally, then cascades the changes to the original table using the correct SQL statement.

{: .note }
> The data is brought together from the client application into the database server (at one-go). It then gets processed together at the same time.

The other bulk operations can be optimized further by targeting the underlying table indexes (via qualifiers). Pass a list of [Field](/class/field) objects when calling the operations.

## Pseudo Table Type

The [Db2BulkImportPseudoTableType](/enumeration/db2/db2bulkimportpseudotabletype) enum lets you choose between a session-private temporary table (`Memory`), an ordinary heap table (`Physical`), or let the library decide based on row count (`Auto`, the default).

{: .important }
> `Auto` and `Memory` both currently resolve to `Physical` at runtime — the internal auto-resolution logic returns `Physical` on every outcome, so there is no session-private staging path implemented yet despite the enum advertising one. Because a physical pseudo-table has no per-session isolation, avoid running concurrent bulk operations against the same target table until this is resolved. Also note that every bulk call creates its own staging table (`CREATE TABLE ... DEFINITION ONLY`) and drops it once the call completes, rather than creating one per (table, pseudo table type) and reusing it across calls — since `CREATE TABLE`/`DROP TABLE` are DDL and commonly force a commit boundary, this happens on every single call, not just the first.

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
> [BulkInsert](/operation/db2/bulkinsert) writes directly into the target table and skips the staging table entirely — unless `identityBehavior` is set to `ReturnIdentity`, in which case a staging table is used first (see above).

#### For BulkDelete / BulkDeleteByKey

```csharp
> DELETE FROM "OriginalTable" T
> WHERE EXISTS (
>     SELECT 1 FROM "PseudoTempTable" S
>     WHERE T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2
> );
```

{: .note }
> Unlike Oracle, Db2 supports a plain correlated `EXISTS` subquery against the staging table, so there is no need for the `ROWID`-matching workaround that Oracle's `DELETE` requires.

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

{: .important }
> The single `MERGE` above is only used when `identityBehavior` is **not** `ReturnIdentity`. When it is, the library instead runs three separate statements — see [Identity Setting Alignment](#identity-setting-alignment) below.

#### For BulkUpdate

```csharp
> MERGE INTO "OriginalTable" T USING "PseudoTempTable" S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> WHEN MATCHED THEN
> UPDATE SET T.Field3 = S.Field3, T.Field4 = S.Field4;
```

{: .note }
> Unlike [BulkMerge](/operation/db2/bulkmerge), there is no `WHEN NOT MATCHED` branch — staged rows with no matching target row are left as-is, not inserted.

## Special Arguments

The arguments below are available on most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the fields used to match existing rows, corresponding to the `WHERE` clause. Defaults to the primary key when not provided. |
| `identityBehavior` | Via [Db2BulkImportIdentityBehavior](/enumeration/db2/db2bulkimportidentitybehavior), controls whether the identity property is kept as-is, or whether the newly generated identity values are returned back to the entities after [BulkInsert](/operation/db2/bulkinsert) or [BulkMerge](/operation/db2/bulkmerge). |
| `pseudoTableType` | Via [Db2BulkImportPseudoTableType](/enumeration/db2/db2bulkimportpseudotabletype), controls the kind of staging table created — see [Pseudo Table Type](#pseudo-table-type) above. |
| `batchSize` | Overrides the number of rows sent to the server per batch. When not set, all items are sent at once. |

## Identity Setting Alignment

When `identityBehavior` is set to `ReturnIdentity`, the library adds a `__RepoDbBulkRowOrder__` identity column to the pseudo-table to track each entity's position in the source [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0).

For [BulkInsert](/operation/db2/bulkinsert), the library runs a single `SELECT identity FROM FINAL TABLE (INSERT INTO ... SELECT ... FROM "PseudoTempTable" ORDER BY __RepoDbBulkRowOrder__) ORDER BY identity` statement, then assigns the returned identities back to the entities in that order.

{: .important }
> For [BulkMerge](/operation/db2/bulkmerge), the single-statement `MERGE` cannot both branch on match and read back generated identities in Db2, so the library instead runs three separate statements: (1) a `LEFT JOIN` snapshot between the staging and target table to classify each staged row as matched or unmatched, (2) a `MERGE ... WHEN MATCHED THEN UPDATE` for the matched rows, and (3) an insert-only statement (again via `FINAL TABLE`) for the unmatched rows to read back their new identities. This is not atomic — three round-trips instead of one — and the matched/unmatched classification can go stale if another connection modifies the target table between the snapshot and the follow-up statements.

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
using (var connection = new DB2Connection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = connection.BulkDelete<Person>(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## BulkDeleteByKey

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var primaryKeys = new object[] { 10045, 10046, 10047 };
    var deletedRows = connection.BulkDeleteByKey("Person", primaryKeys);
}
```

## BulkInsert

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people);
}
```

To return the newly generated identity values:

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: Db2BulkImportIdentityBehavior.ReturnIdentity);
}
```

## BulkMerge

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var mergedRows = connection.BulkMerge(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

## BulkUpdate

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate(people);
}
```

Or with qualifiers.

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var updatedRows = connection.BulkUpdate(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```
