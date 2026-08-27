---
layout: default
title: "Firebird"
has_children: true
permalink: /operation/firebird
parent: OPERATIONS
---

# Operations (Firebird)

---

RepoDB's standard operations ([Query](/operation/query), [Insert](/operation/insert), [Merge](/operation/merge), [Update](/operation/update), [Delete](/operation/delete), etc.) all work against `FbConnection` once [UseFirebird()](/class/firebird/firebirdconfiguration) has been called. `BulkInsert`, `BulkMerge`, `BulkUpdate`, `BulkDelete` and `BulkDeleteByKey` are provided by the separate [RepoDb.Firebird.BulkOperations](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations) package.

## Bulk Operations

`BulkMerge`, `BulkUpdate`, `BulkDelete` and `BulkDeleteByKey` stage the incoming data into a uniquely-named pseudo (staging) table before cascading the change to the real table; `BulkInsert` writes straight into the target table unless [FirebirdBulkImportIdentityBehavior.ReturnIdentity](/enumeration/firebird/firebirdbulkimportidentitybehavior) is requested, in which case it also routes through a staging table so the generated identity values can be read back.

- [FirebirdBulkImportIdentityBehavior](/enumeration/firebird/firebirdbulkimportidentitybehavior) — controls whether the identity property is kept as-is (`KeepIdentity`, the default) or read back after the operation (`ReturnIdentity`).
- [FirebirdBulkImportPseudoTableType](/enumeration/firebird/firebirdbulkimportpseudotabletype) — controls whether the staging table is a Firebird `GLOBAL TEMPORARY TABLE` (`Memory`), an ordinary heap table (`Physical`), or chosen automatically by row count (`Auto`, the default). Since every staging table is created with a per-call unique name, `Memory` and `Physical` are both safe under concurrent callers writing against the same target table.

## SQL generation notes

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
