---
layout: default
title: "SAP HANA"
has_children: true
permalink: /operation/saphana
parent: OPERATIONS
---

# Operations (SAP HANA)

---

RepoDB's standard operations ([Query](/operation/query), [Insert](/operation/insert), [Merge](/operation/merge), [Update](/operation/update), [Delete](/operation/delete), etc.) all work against `HanaConnection` once [UseSapHana()](/class/saphana/saphanaconfiguration) has been called. `BulkInsert`, `BulkMerge`, `BulkUpdate`, `BulkDelete` and `BulkDeleteByKey` are provided by the separate [RepoDb.SapHana.BulkOperations](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations) package.

{: .important }
> Unlike every other bulk-operations package in this codebase, SAP HANA has no native bulk-load API to build on — `Sap.Data.Hana` exposes no equivalent of `SqlBulkCopy`/`DB2BulkCopy`/`OracleBulkCopy`, and HANA's own SQL parser rejects a multi-row `INSERT ... VALUES (...), (...)` list. Every "bulk" write here is really a client-buffered loop of single-row, parameterized `INSERT` statements — one round trip per row — against the real or pseudo table. `batchSize` (default `500`) only controls how many rows are buffered client-side between flushes; it is not a bind-parameter or round-trip batching knob the way it is for the other providers.

For [BulkDelete](/operation/saphana/bulkdelete), [BulkDeleteByKey](/operation/saphana/bulkdeletebykey), [BulkMerge](/operation/saphana/bulkmerge) and [BulkUpdate](/operation/saphana/bulkupdate), a pseudo (staging) table is created — and dropped — for every call, indexed on the qualifier columns. The library writes to it via the row-by-row loop above, then cascades the changes to the original table using the correct SQL statement. [BulkInsert](/operation/saphana/bulkinsert) writes straight into the target table unless [SapHanaBulkImportIdentityBehavior.ReturnIdentity](/enumeration/saphana/saphanabulkimportidentitybehavior) is requested, in which case a pseudo table is used first so the generated identity values can be read back.

{: .important }
> Unlike Db2, Firebird, Oracle, and Vertica, SAP HANA's pseudo table is created under a **deterministic name** — `{pseudoTableType}{tableName}{operation}` (e.g. `PhysicalPersonMerge`) — not a per-call-unique one. Combined with [SapHanaBulkImportPseudoTableType](/enumeration/saphana/saphanabulkimportpseudotabletype)'s `Auto`/`Memory` both currently resolving to `Physical` (the only kind actually implemented), two concurrent bulk calls of the same operation against the same table can interfere with each other's staged rows. Avoid running concurrent SAP HANA bulk operations of the same kind against the same table until session-isolated `Memory` staging is implemented.

The other bulk operations can be optimized further by targeting the underlying table indexes (via qualifiers). Pass a list of [Field](/class/field) objects when calling the operations.

## Pseudo Table Type

See [SapHanaBulkImportPseudoTableType](/enumeration/saphana/saphanabulkimportpseudotabletype) for the full detail on `Auto`, `Memory`, and `Physical`, and the concurrency caveat above.

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
> [BulkInsert](/operation/saphana/bulkinsert) writes directly into the target table and skips the staging table entirely — unless `identityBehavior` is set to `ReturnIdentity`, in which case a staging table is used first (see above).

#### For BulkDelete / BulkDeleteByKey

```csharp
> DELETE FROM "OriginalTable" WHERE EXISTS (
>     SELECT 1 FROM "PseudoTempTable" S
>     WHERE "OriginalTable".QualifierField1 = S.QualifierField1 AND "OriginalTable".QualifierField2 = S.QualifierField2
> );
```

#### For BulkMerge

A real, single-statement ANSI `MERGE` — no anti-join workaround is needed here, unlike MySQL:

```csharp
> MERGE INTO "OriginalTable" T USING "PseudoTempTable" S ON (T.QualifierField1 = S.QualifierField1 AND T.QualifierField2 = S.QualifierField2)
> WHEN MATCHED THEN
> UPDATE SET T.Field3 = S.Field3, T.Field4 = S.Field4
> WHEN NOT MATCHED THEN
> INSERT (Field1, Field2, ...) VALUES (S.Field1, S.Field2, ...);
```

{: .note }
> The identity column, if any, is always left out of the `INSERT` column list — a brand-new row's identity property is typically an unset default (e.g. `0`), not a real value meant to be inserted as-is. When `identityBehavior` is `ReturnIdentity`, a different three-step statement sequence is used instead — see [BulkMerge](/operation/saphana/bulkmerge#identity-setting-alignment).

#### For BulkUpdate

SAP HANA has no multi-table `UPDATE ... JOIN`, so a correlated subquery assigns every updateable column at once:

```csharp
> UPDATE "OriginalTable" SET ("Field3", "Field4") = (
>     SELECT S."Field3", S."Field4" FROM "PseudoTempTable" S
>     WHERE "OriginalTable".QualifierField1 = S.QualifierField1 AND "OriginalTable".QualifierField2 = S.QualifierField2
> ) WHERE EXISTS (
>     SELECT 1 FROM "PseudoTempTable" S
>     WHERE "OriginalTable".QualifierField1 = S.QualifierField1 AND "OriginalTable".QualifierField2 = S.QualifierField2
> );
```

{: .note }
> Unlike [BulkMerge](/operation/saphana/bulkmerge), there is no `WHEN NOT MATCHED` branch — staged rows with no matching target row are left as-is, not inserted.

## Special Arguments

The arguments below are available on most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the fields used to match existing rows. Defaults to the primary or identity column when not provided. |
| `identityBehavior` | Via [SapHanaBulkImportIdentityBehavior](/enumeration/saphana/saphanabulkimportidentitybehavior), controls whether the identity property is kept as-is, or whether newly generated identity values are returned back to the entities after [BulkInsert](/operation/saphana/bulkinsert) or [BulkMerge](/operation/saphana/bulkmerge). |
| `pseudoTableType` | Via [SapHanaBulkImportPseudoTableType](/enumeration/saphana/saphanabulkimportpseudotabletype), controls the kind of staging table created — see [Pseudo Table Type](#pseudo-table-type) above. |
| `batchSize` | Overrides how many rows are buffered client-side between flushes. Defaults to `500`. This does **not** change the number of round trips — every row is still its own `INSERT`. |

## Async Methods

All the provided synchronous operations have an equivalent asynchronous (`Async`) counterpart.
