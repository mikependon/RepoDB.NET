---
layout: default
title: "MySqlConnector"
nav_order: 6
has_children: true
permalink: /operation/mysqlconnector
parent: OPERATIONS
---

# Operations (MySqlConnector)

---

For MySQL via the [MySqlConnector](https://www.nuget.org/packages/MySqlConnector) driver, the underlying implementation is leveraging the [MySqlBulkCopy](https://mysqlconnector.net/api/mysqlconnector/mysqlbulkcopytype/) class of the [MySqlConnector](https://www.nuget.org/packages/MySqlConnector) namespace — the same kind of bulk-load primitive [SqlBulkCopy](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqlbulkcopy?view=dotnet-plat-ext-7.0) is for SQL Server.

For [BulkInsert](/operation/mysqlconnector/bulkinsert), the entities/rows are written straight to the target table — unless [MySqlConnectorBulkImportIdentityBehavior.ReturnIdentity](/enumeration/mysqlconnector/mysqlconnectorbulkimportidentitybehavior) is requested, in which case a staging (pseudo) table is used instead, so the newly generated `AUTO_INCREMENT` values can be pre-assigned and read back before the rows are copied into the target table.

For [BulkDelete](/operation/mysqlconnector/bulkdelete), [BulkDeleteByKey](/operation/mysqlconnector/bulkdeletebykey), [BulkMerge](/operation/mysqlconnector/bulkmerge) and [BulkUpdate](/operation/mysqlconnector/bulkupdate), a pseudo (staging) table is created under a transaction context. The library writes to it via [BulkInsert](/operation/mysqlconnector/bulkinsert) internally, then cascades the changes to the original table using the correct SQL statement, then drops the pseudo table.

{: .note }
> The data is brought together from the client application into the database server (at one-go). It then gets processed together at the same time.

The other bulk operations can be optimized further by targeting the underlying table indexes (via qualifiers). Pass a list of [Field](/class/field) objects when calling the operations.

## Pseudo Table Type

The [MySqlConnectorBulkImportPseudoTableType](/enumeration/mysqlconnector/mysqlconnectorbulkimportpseudotabletype) enum lets you choose between a `TEMPORARY` table (session-isolated) and an ordinary heap table for the staging table.

{: .important }
> Every value currently resolves to `Physical` at runtime, regardless of the value passed or the row count. `Auto` and `Memory` are kept in the enum so existing code doesn't need to change once a session-isolated staging table is fully wired up. Avoid running concurrent bulk operations against the same target table until this is resolved.

## Supported Objects

Below are the following objects supported by the bulk operations.

- System.DataTable
- System.Data.Common.DbDataReader
- IEnumerable&lt;T&gt;
- ExpandoObject
- IDictionary&lt;string, object&gt;

## Operation SQL Statements

Once all the data is in the pseudo (staging) table, the correct SQL statement is used to cascade the changes towards the original table.

{: .note }
> [BulkInsert](/operation/mysqlconnector/bulkinsert) writes directly into the target table and skips the staging table entirely — unless `identityBehavior` is set to `ReturnIdentity`, in which case a staging table is used to pre-assign identity values first (see [Identity Setting Alignment](#identity-setting-alignment) below).

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
> MySQL has no `MERGE` statement. `BulkMerge` is instead split into an `UPDATE ... INNER JOIN` for rows that match on the qualifiers, followed by an `INSERT ... SELECT` guarded by a `LEFT JOIN ... WHERE ... IS NULL` anti-join for rows that don't. The identity column, if any, is always left out of the `INSERT` column list — a brand-new row's identity property is typically an unset default (e.g. `0`), not a real value meant to be inserted as-is.

#### For BulkUpdate

```csharp
> UPDATE `OriginalTable` T INNER JOIN `PseudoTempTable` S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> SET T.Field3 = S.Field3, T.Field4 = S.Field4;
```

{: .note }
> Unlike [BulkMerge](/operation/mysqlconnector/bulkmerge), there is no anti-join `INSERT` branch — staged rows with no matching target row are left as-is, not inserted. If every staged column is also a qualifier (i.e. there is nothing left to update), the operation short-circuits and returns `0` without touching the database.

## Special Arguments

The arguments below are available on most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the fields used to match existing rows, corresponding to the `WHERE`/`ON` clause. Defaults to the primary key when not provided. |
| `identityBehavior` | Via [MySqlConnectorBulkImportIdentityBehavior](/enumeration/mysqlconnector/mysqlconnectorbulkimportidentitybehavior), controls whether the identity column is left for `AUTO_INCREMENT` to generate, or whether the newly generated (or matched) identity values are returned back to the entities after [BulkInsert](/operation/mysqlconnector/bulkinsert) or [BulkMerge](/operation/mysqlconnector/bulkmerge). |
| `pseudoTableType` | Via [MySqlConnectorBulkImportPseudoTableType](/enumeration/mysqlconnector/mysqlconnectorbulkimportpseudotabletype), controls the kind of staging table created — see [Pseudo Table Type](#pseudo-table-type) above. |
| `batchSize` | Overrides the number of rows sent to the server per batch. When not set, all items are sent at once. |
| `mappings` | Via `MySqlConnectorBulkInsertMapItem`, defines explicit column mappings between the source properties/columns and the destination columns. When omitted, columns are auto-mapped by name (case-insensitive). |
| `bulkCopyTimeout` | Overrides the command timeout, in seconds. |

## Identity Setting Alignment

When `identityBehavior` is set to `ReturnIdentity`, the library adds a surrogate `__RepoDbBulkRowOrder__` column to the pseudo table to track each entity's/row's position in the source [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) — MySQL has no equivalent to Oracle's implicit `ROWID`, so this stands in for it. Identity values are pre-assigned into the pseudo table via a session user variable seeded from the target table's current `MAX(identity) + 1`, then copied over as literal values, since MySQL's `AUTO_INCREMENT` has no per-row `NEXTVAL`-style mechanism to pull values from ahead of the actual `INSERT`. The resultset is then read back ordered by `__RepoDbBulkRowOrder__` so each value is assigned to the correct entity, regardless of ordering during the underlying `INSERT`/`MERGE`.

{: .important }
> Returning identity values relies on session user variables (and, for `BulkInsert`/`BulkMerge`'s nullable-column step, a dynamically prepared `ALTER TABLE` statement). Add `AllowUserVariables=True` to your MySqlConnector connection string when using `identityBehavior: ReturnIdentity` — it is `false` by default — otherwise the operation will fail.

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
        identityBehavior: MySqlConnectorBulkImportIdentityBehavior.ReturnIdentity);
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
