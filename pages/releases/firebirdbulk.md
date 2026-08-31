---
layout: default
sidebar: releases
title: Firebird (Bulk)
description: "This page contains the latest information of the releases of RepoDb.Firebird.BulkOperations library."
permalink: /release/firebirdbulk
parent: RELEASES
---

# Releases for RepoDb.Firebird.BulkOperations

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.Firebird.BulkOperations).

## RepoDb.Firebird.BulkOperations (v0.0.1-alpha) - Preview

Released: 2026-08-29

New
{: .label .label-green }

First release of the bulk operations extension for [RepoDb.Firebird](/release/firebird), built on `FbBatchCommand` — the FirebirdSql.Data.FirebirdClient driver's native ADO.NET batching API — via [FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher).

> **Verification status:** this package has been implemented and reviewed, but not yet exercised against a live Firebird instance. Verify the pseudo-table lifecycle, the `EXECUTE BLOCK` identity read-back paths, and the transactional behavior end-to-end before relying on this package in production.

### What's included

- [BulkInsert](/operation/firebird/bulkinsert), [BulkMerge](/operation/firebird/bulkmerge), [BulkUpdate](/operation/firebird/bulkupdate), [BulkDelete](/operation/firebird/bulkdelete) and [BulkDeleteByKey](/operation/firebird/bulkdeletebykey), each with an `Async` overload, callable against an `FbConnection` with an entity list, a `DataTable`, or an `IDataReader` (`BulkDeleteByKey` takes a list of primary key values instead).
- [FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher) — an `FbBatchCommand`-based bulk-copy class with true asynchronous capability (`FbBatchCommand.ExecuteNonQueryAsync`), used internally to write into the target table (`BulkInsert` without `ReturnIdentity`) and into the pseudo (staging) table backing the other operations.
- `FirebirdBulkImportIdentityBehavior` (`KeepIdentity` default, `ReturnIdentity`) — controls whether identity values are sent as-is or read back. `ReturnIdentity` reads generated values back via an `EXECUTE BLOCK` cursor loop over the staging table, correlated to the source rows by a client-assigned row-order column rather than by sorting the generated identities themselves.
- `FirebirdBulkImportPseudoTableType` (`Auto`, `Memory`, `Physical`) — selects the staging-table strategy backing `BulkMerge`, `BulkUpdate`, `BulkDelete`, `BulkDeleteByKey`, and `BulkInsert` (when `ReturnIdentity` is used). Unlike some other providers' bulk-operations packages, every staging table is created with a per-call unique name, so `Memory` and `Physical` are both genuinely functional and safe under concurrent callers writing against the same target table.
- [FirebirdCommandBatcherMapItem](/class/firebird/firebirdcommandbatchermapitem) — explicit source-to-destination column mapping, with an optional `FbDbType` override.
- [FirebirdCommandBatcherColumnMappingCollection](/class/firebird/firebirdcommandbatchercolumnmappingcollection) — the `ColumnMappings` collection backing `FirebirdCommandBatcher`.
- [FirebirdTraceKeys](/class/firebird/firebirdtracekeys) — the tracing key constants for the five bulk operations, for use with [ITrace](/interface/itrace).
- `BulkMerge` generates one of three statement shapes depending on whether the identity column is itself a qualifier and whether `ReturnIdentity` is requested: a single ANSI `MERGE`, an `EXECUTE BLOCK` loop of `UPDATE OR INSERT ... RETURNING`, or an `EXECUTE BLOCK` loop that branches per row between a plain `INSERT` and `UPDATE OR INSERT` — see [BulkMerge](/operation/firebird/bulkmerge#operation-sql-statements) for details.
- When a caller-supplied `FbTransaction` is passed, every step of a bulk operation's pipeline (pseudo-table DDL, the batched write, the cascading statement, and the pseudo-table drop) participates in it — and since Firebird's DDL is itself transactional, the whole pipeline is genuinely atomic in that case, unlike providers whose DDL forces an implicit commit.

### Known limitations (v1)

- Without an explicit caller-supplied `transaction`, each step of a bulk operation's pipeline runs under its own implicit transaction, so a failure partway through (e.g. after the pseudo table is created but before it is dropped) can leave an orphaned pseudo table behind.
- `BulkCopyTimeout` is accepted on every operation for signature symmetry with the other providers' bulk-operations packages, but `FbBatchCommand` has no timeout-equivalent property to apply it to — the argument currently has no effect.
- `BulkMerge`/`BulkUpdate`/`BulkDelete`/`BulkDeleteByKey` report Firebird's engine-level affected-row count where available, but `MERGE`/`EXECUTE BLOCK` statements always report `-1` from `ExecuteNonQuery` — for those, the pseudo table's own row count is used as a proxy instead, on the assumption that every staged row is guaranteed to be either inserted, updated, or matched for deletion.
- The `DbDataReader` overloads of `BulkInsert` and `BulkMerge` have no `identityBehavior` argument — a forward-only, single-pass reader cannot be rewound to correlate generated identity values back onto a source row.
- `BulkUpdate` returns `0` immediately, without creating a pseudo table at all, when every staged field is also a qualifier (i.e. there is nothing left to actually update).
- Every bulk call against a table it hasn't seen before creates its own uniquely-named staging table (`CREATE TABLE`/`CREATE GLOBAL TEMPORARY TABLE`) and drops it once the call completes, rather than creating one per (table, pseudo table type) and reusing it across calls the way the Oracle bulk package does — trading a small amount of per-call DDL overhead for full safety under concurrent callers.
- This package inherits every [Known limitation of RepoDb.Firebird](/release/firebird) itself (e.g. no table hints, `MaxParameterCount` of `1500`, Firebird 3.0+ required for identity detection).

- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `RepoDb.Firebird` package `v0.0.1-alpha`.
- Referenced the `FirebirdSql.Data.FirebirdClient` package `v10.3.4`.
