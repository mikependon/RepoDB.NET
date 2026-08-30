---
layout: default
title: "Vertica"
has_children: true
permalink: /operation/vertica
parent: OPERATIONS
---

# Operations (Vertica)

---

RepoDB's standard operations ([Query](/operation/query), [Insert](/operation/insert), [Merge](/operation/merge), [Update](/operation/update), [Delete](/operation/delete), etc.) all work against `VerticaConnection` once [UseVertica()](/class/vertica/verticaconfiguration) has been called. `BulkInsert`, `BulkMerge`, `BulkUpdate`, `BulkDelete` and `BulkDeleteByKey` are provided by the separate [RepoDb.Vertica.BulkOperations](https://www.nuget.org/packages/RepoDb.Vertica.BulkOperations) package, built on `VerticaCopyStream` — `Vertica.Data`'s native `COPY ... FROM STDIN` streaming API.

For [BulkDelete](/operation/vertica/bulkdelete), [BulkDeleteByKey](/operation/vertica/bulkdeletebykey), [BulkMerge](/operation/vertica/bulkmerge) and [BulkUpdate](/operation/vertica/bulkupdate), a pseudo (staging) table is created — and dropped — for every call, indexed on the qualifier columns. The library streams into it via a `COPY` load internally, then cascades the changes to the original table using the correct SQL statement.

For [BulkInsert](/operation/vertica/bulkinsert), rows are streamed straight to the target table — unless [VerticaBulkImportIdentityBehavior.ReturnIdentity](/enumeration/vertica/verticabulkimportidentitybehavior) is requested, in which case a pseudo table is used first so the generated identity values can be read back.

{: .note }
> Every pseudo table gets a per-call unique name, so unlike some other providers' bulk-operations packages, concurrent callers writing against the same target table never race on a shared staging-table name.

The other bulk operations can be optimized further by targeting the underlying table indexes (via qualifiers). Pass a list of [Field](/class/field) objects when calling the operations.

## Pseudo Table Type

The [VerticaBulkImportPseudoTableType](/enumeration/vertica/verticabulkimportpseudotabletype) enum lets you choose between a Vertica `GLOBAL TEMPORARY TABLE` (`Memory`), an ordinary heap table (`Physical`), or let the library decide based on row count (`Auto`, the default — `Physical` at 5,000 rows or more, otherwise `Memory`).

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
> [BulkInsert](/operation/vertica/bulkinsert) writes directly into the target table and skips the staging table entirely — unless `identityBehavior` is set to `ReturnIdentity`, in which case a staging table is used first (see above).

#### For BulkDelete / BulkDeleteByKey

```csharp
> DELETE FROM "OriginalTable"
> WHERE EXISTS (
>     SELECT 1 FROM "PseudoTempTable" S
>     WHERE "OriginalTable".QualifierField1 = S.QualifierField1 AND "OriginalTable".QualifierField2 = S.QualifierField2
> );
```

#### For BulkMerge

Vertica flatly refuses to run a `MERGE` statement at all against a table that has an `IDENTITY`/`AUTO_INCREMENT` column, so a bulk merge is always expressed as two separate statements against the pseudo table — never a native `MERGE`.

```csharp
> UPDATE "OriginalTable" SET Field3 = S.Field3, Field4 = S.Field4
> FROM "PseudoTempTable" S
> WHERE "OriginalTable".QualifierField1 = S.QualifierField1 AND "OriginalTable".QualifierField2 = S.QualifierField2;
>
> INSERT INTO "OriginalTable" (Field1, Field2, ...)
> SELECT S.Field1, S.Field2, ... FROM "PseudoTempTable" S
> WHERE NOT EXISTS (
>     SELECT 1 FROM "OriginalTable"
>     WHERE "OriginalTable".QualifierField1 = S.QualifierField1 AND "OriginalTable".QualifierField2 = S.QualifierField2
> )
> ORDER BY S.__RepoDbBulkRowOrder__;
```

{: .note }
> The `UPDATE` step is skipped entirely when there are no non-qualifier, non-identity fields left to update. Unlike the plain (non-bulk) [Merge](/operation/merge) operation, these two statements are executed as two separate round trips — not joined into one compound command text — so `IsMultiStatementExecutable` being `false` does not affect this path.

#### For BulkUpdate

```csharp
> UPDATE "OriginalTable" SET Field3 = S.Field3, Field4 = S.Field4
> FROM "PseudoTempTable" S
> WHERE "OriginalTable".QualifierField1 = S.QualifierField1 AND "OriginalTable".QualifierField2 = S.QualifierField2;
```

{: .note }
> Unlike [BulkMerge](/operation/vertica/bulkmerge), there is no `INSERT` step — staged rows with no matching target row are left as-is, not inserted.

## Special Arguments

The arguments below are available on most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the fields used to match existing rows. Defaults to the primary or identity column when not provided. |
| `identityBehavior` | Via [VerticaBulkImportIdentityBehavior](/enumeration/vertica/verticabulkimportidentitybehavior), controls whether the identity property is kept as-is, or whether newly generated identity values are returned back to the entities after [BulkInsert](/operation/vertica/bulkinsert) or [BulkMerge](/operation/vertica/bulkmerge). |
| `pseudoTableType` | Via [VerticaBulkImportPseudoTableType](/enumeration/vertica/verticabulkimportpseudotabletype), controls the kind of staging table created — see [Pseudo Table Type](#pseudo-table-type) above. |
| `batchSize` | Overrides the number of rows sent to the server per batch. When not set, all items are sent at once. |

## BatchSize

All the provided bulk operations have a `batchSize` argument that lets you override the number of rows wired-up to the server per batch. By default it is `null`, meaning all items are sent together in one go.

Use this argument if you wish to optimize the operation based on certain situations.

- Network Latency
- Infrastructure
- No. of Columns
- Type of Data

## No table hints

`VerticaDbSetting.AreTableHintsSupported` is `false`. Passing a non-null `hints` argument to any operation throws a `NotSupportedException`.

## No true multi-statement execution, except for InsertAll

`VerticaDbSetting.IsMultiStatementExecutable` is `false` — `VerticaCommand` refuses to execute a compound `;`-separated statement once it carries a parameter. [MergeAll](/operation/mergeall)/[UpdateAll](/operation/updateall) issue one statement per row instead of a single batched command; passing an explicit `batchSize` greater than `1` to either throws a `NotSupportedException`. [InsertAll](/operation/insertall) is the exception — `VerticaDbSetting.IsInsertAllBatchable` is `true`, so it still batches multiple rows into one genuine multi-row `INSERT ... VALUES (...), (...), ...` statement.

{: .important }
> The plain (non-bulk) [Merge](/operation/merge)/[MergeAll](/operation/mergeall) operations compile an `UPDATE ...; INSERT ...` compound statement carrying parameters in both halves — this appears to conflict with the restriction above, and has not been verified against a live Vertica instance. See [VerticaStatementBuilder](/class/vertica/verticastatementbuilder) for details.

## Async Methods

All the provided synchronous operations have an equivalent asynchronous (`Async`) counterpart.
