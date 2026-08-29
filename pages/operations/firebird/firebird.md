---
layout: default
title: "Firebird"
has_children: true
permalink: /operation/firebird
parent: OPERATIONS
---

# Operations (Firebird)

---

RepoDB's standard operations ([Query](/operation/query), [Insert](/operation/insert), [Merge](/operation/merge), [Update](/operation/update), [Delete](/operation/delete), etc.) all work against `FbConnection` once [UseFirebird()](/class/firebird/firebirdconfiguration) has been called. [BulkInsert](/operation/firebird/bulkinsert), [BulkMerge](/operation/firebird/bulkmerge), [BulkUpdate](/operation/firebird/bulkupdate), [BulkDelete](/operation/firebird/bulkdelete) and [BulkDeleteByKey](/operation/firebird/bulkdeletebykey) are provided by the separate [RepoDb.Firebird.BulkOperations](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations) package, built on `FbBatchCommand` — the FirebirdSql.Data.FirebirdClient driver's native ADO.NET batching API — via [FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher).

For [BulkInsert](/operation/firebird/bulkinsert), rows are written straight to the target table — unless [FirebirdBulkImportIdentityBehavior.ReturnIdentity](/enumeration/firebird/firebirdbulkimportidentitybehavior) is requested, in which case a pseudo (staging) table is used instead so the generated identity values can be read back via an `EXECUTE BLOCK` cursor loop, correlated to the source rows by a client-assigned row-order column rather than by sorting the generated identities themselves.

For [BulkDelete](/operation/firebird/bulkdelete), [BulkDeleteByKey](/operation/firebird/bulkdeletebykey), [BulkMerge](/operation/firebird/bulkmerge) and [BulkUpdate](/operation/firebird/bulkupdate), a pseudo (staging) table is created — and dropped — for every call, indexed on the qualifier columns. The library writes to it via [FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher) internally, then cascades the changes to the original table using the correct SQL statement.

{: .note }
> Every pseudo table gets a per-call unique name, so unlike some other providers' bulk-operations packages, concurrent callers writing against the same target table never race on a shared staging-table name.

The other bulk operations can be optimized further by targeting the underlying table indexes (via qualifiers). Pass a list of [Field](/class/field) objects when calling the operations.

## Pseudo Table Type

The [FirebirdBulkImportPseudoTableType](/enumeration/firebird/firebirdbulkimportpseudotabletype) enum lets you choose between a Firebird `GLOBAL TEMPORARY TABLE` (`Memory`), an ordinary heap table (`Physical`), or let the library decide based on row count (`Auto`, the default — `Physical` at 5,000 rows or more, otherwise `Memory`).

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
> [BulkInsert](/operation/firebird/bulkinsert) writes directly into the target table and skips the staging table entirely — unless `identityBehavior` is set to `ReturnIdentity`, in which case a staging table is used first (see above).

#### For BulkDelete / BulkDeleteByKey

```csharp
> DELETE FROM "OriginalTable" T
> WHERE EXISTS (
>     SELECT 1 FROM "PseudoTempTable" S
>     WHERE T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2
> );
```

#### For BulkMerge

The exact shape depends on whether the identity column (if any) is itself a merge qualifier — see [BulkMerge](/operation/firebird/bulkmerge#operation-sql-statements) for the three variants (a single `MERGE`, or one of two `EXECUTE BLOCK` loop shapes).

#### For BulkUpdate

```csharp
> MERGE INTO "OriginalTable" T USING "PseudoTempTable" S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> WHEN MATCHED THEN
> UPDATE SET T.Field3 = S.Field3, T.Field4 = S.Field4;
```

{: .note }
> Unlike [BulkMerge](/operation/firebird/bulkmerge), there is no `WHEN NOT MATCHED` branch — staged rows with no matching target row are left as-is, not inserted.

## Special Arguments

The arguments below are available on most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the fields used to match existing rows. Defaults to the primary or identity column when not provided. |
| `identityBehavior` | Via [FirebirdBulkImportIdentityBehavior](/enumeration/firebird/firebirdbulkimportidentitybehavior), controls whether the identity property is kept as-is, or whether newly generated identity values are returned back to the entities after [BulkInsert](/operation/firebird/bulkinsert) or [BulkMerge](/operation/firebird/bulkmerge). |
| `pseudoTableType` | Via [FirebirdBulkImportPseudoTableType](/enumeration/firebird/firebirdbulkimportpseudotabletype), controls the kind of staging table created — see [Pseudo Table Type](#pseudo-table-type) above. |
| `batchSize` | Overrides the number of rows sent to the server per batch. When not set, all items are sent at once. |

## BatchSize

All the provided bulk operations have a `batchSize` argument that lets you override the number of rows wired-up to the server per batch. By default it is `null`, meaning all items are sent together in one go.

Use this argument if you wish to optimize the operation based on certain situations.

- Network Latency
- Infrastructure
- No. of Columns
- Type of Data

## SQL generation notes (standard operations)

[FirebirdStatementBuilder](/class/firebird/firebirdstatementbuilder) generates Firebird-flavored SQL for every operation:

- `FIRST n` for top-N [Query](/operation/query)/[Exists](/operation/exists), and `FIRST m SKIP n` for [BatchQuery](/operation/batchquery)/skip-take queries — Firebird has no `TOP`/`LIMIT` keyword.
- [Insert](/operation/insert)/[Merge](/operation/merge) read back the generated key via Firebird's native `RETURNING` clause, which (unlike Oracle) surfaces directly as an ordinary single-row result set — no PL/SQL block or output-parameter wrapping needed.
- [Merge](/operation/merge)/[MergeAll](/operation/mergeall) compile to `UPDATE OR INSERT INTO ... MATCHING (...) RETURNING ...`, Firebird's native single-statement upsert. When the identity column is itself a qualifier, an `EXECUTE BLOCK` is used instead (see [FirebirdStatementBuilder](/class/firebird/firebirdstatementbuilder) for details), since `MATCHING` cannot reliably match a not-yet-inserted row on its own not-yet-known identity value.
- [Truncate](/operation/truncate) compiles to a plain `DELETE FROM t` — Firebird has no `TRUNCATE TABLE` statement (as of 5.0) — which does not reset a `GENERATED ... AS IDENTITY` column's next value.

## No table hints

`FirebirdDbSetting.AreTableHintsSupported` is `false`. Passing a non-null `hints` argument to any operation throws a `NotSupportedException`.

## One row per round trip for the *All operations

`FirebirdDbSetting.IsMultiStatementExecutable` is `false` — `FbCommand` cannot execute multiple statements in a single round trip. [InsertAll](/operation/insertall), [MergeAll](/operation/mergeall) and [UpdateAll](/operation/updateall) issue one statement per row instead of a single batched command; passing an explicit `batchSize` greater than `1` to any of them throws a `NotSupportedException`.

## No session-wide scope identity

[GetScopeIdentity](/class/firebird/firebirddbhelper)/`GetScopeIdentityAsync` always throw `NotSupportedException` — Firebird has no construct equivalent to SQL Server's `SCOPE_IDENTITY()` or MySQL's `LAST_INSERT_ID()`. The generated key is already returned directly by [Insert](/operation/insert)/[Merge](/operation/merge) via `RETURNING`; query the underlying generator explicitly (e.g. `GEN_ID(generator_name, 0)`) if you need it out-of-band.

## Async Methods

All the provided synchronous operations have an equivalent asynchronous (`Async`) counterpart.
