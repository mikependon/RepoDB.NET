---
layout: default
sidebar: releases
title: SAP HANA (Bulk)
description: "This page contains the latest information of the releases of RepoDb.SapHana.BulkOperations library."
permalink: /release/saphanabulk
parent: RELEASES
---

# Releases for RepoDb.SapHana.BulkOperations

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.SapHana.BulkOperations).

## RepoDb.SapHana.BulkOperations (v0.0.1-alpha) - Preview

Released: TBA

New
{: .label .label-green }

First release of the bulk operations extension for [RepoDb.SapHana](/release/saphana).

> **Verification status:** implemented and reviewed, but not yet exercised against a live SAP HANA instance. Verify the pseudo-table lifecycle, the concurrency caveat below, and the identity-assignment arithmetic end-to-end before relying on this package in production.

> **Architectural note:** SAP HANA has no native bulk-load API to build on, and its SQL parser rejects a multi-row `INSERT ... VALUES (...), (...)` list. Every write here is a client-buffered loop of single-row, parameterized `INSERT` statements — one round trip per row — against the real or pseudo table.

### What's included

- [BulkInsert](/operation/saphana/bulkinsert), [BulkMerge](/operation/saphana/bulkmerge), [BulkUpdate](/operation/saphana/bulkupdate), [BulkDelete](/operation/saphana/bulkdelete) and [BulkDeleteByKey](/operation/saphana/bulkdeletebykey), each with an `Async` overload, callable against a `HanaConnection` with an entity list, a `DataTable`, or an `IDataReader` (`BulkDeleteByKey` takes a target table name and a list of primary key values directly, rather than an entity type parameter).
- A row-by-row write pipeline (an internal `ColumnFilteredDataReader` plus a buffered `INSERT` loop) that buffers rows client-side (`batchSize`, default `500`) to control memory/flush frequency. `bulkCopyTimeout` applies here as each row's `CommandTimeout`.
- `SapHanaBulkImportIdentityBehavior` (`KeepIdentity` default, `ReturnIdentity`) — controls whether identity values are sent as-is or read back. `ReturnIdentity` pre-assigns identities client-side (`MAX(identity) + 1`, offset by the pseudo table's own row-order column) before moving rows into the real table with a single `INSERT ... SELECT`, then reads them back ordered by that same row-order column.
- `SapHanaBulkImportPseudoTableType` (`Auto`, `Memory`, `Physical`) — selects the staging-table strategy backing every operation.
- [SapHanaBulkInsertMapItem](/class/saphana/saphanabulkinsertmapitem) — explicit source-to-destination column mapping, with an optional `HanaDbType` override. Explicit mappings are also type-checked up front: an incompatible source/destination CLR type pairing (other than the allowed `Guid`↔`string` and integral-to-integral widenings) throws an `InvalidTypeException` immediately, rather than failing inside SAP HANA.
- [SapHanaTraceKeys](/class/saphana/saphanatracekeys) — the tracing key constants for the five bulk operations, for use with [ITrace](/interface/itrace).
- [SapHanaCommandBatcher](/class/saphana/saphanacommandbatcher) and [SapHanaCommandBatcherColumnMappingCollection](/class/saphana/saphanacommandbatchercolumnmappingcollection) — since `HanaBulkCopy` has no native async API, the `Async` overloads write through this `HanaCommand`-based row batcher instead, giving genuine asynchronous execution rather than a synchronous `HanaBulkCopy` call offloaded onto a thread-pool thread via `Task.Run`. The sync overloads still use `HanaBulkCopy` directly.
- [SapHanaBulkDbSetting](/class/saphana/saphanabulkdbsetting) and [ISapHanaBulkDbSetting](/interface/saphana/isaphanabulkdbsetting) — an opt-in [SapHanaDbSetting](/class/saphana/saphanadbsetting) subclass (and its interface) exposing `WriteToServerExecution` (via [SapHanaWriteToServerExecution](/enumeration/saphana/saphanawritetoserverexecution)), letting the `Async` overloads fall back to the synchronous `HanaBulkCopy` path instead of [SapHanaCommandBatcher](/class/saphana/saphanacommandbatcher). Not registered by default — pass an instance to `UseSapHana()` or register it via [DbSettingMapper](/mapper/dbsettingmapper) to opt in.
- `SapHanaGuidToStringPropertyHandler` — maps a `Guid` entity property to/from an `NVARCHAR(36)` column, since SAP HANA has no native GUID type. Ships in this package (not the core `RepoDb.SapHana` provider), but is a plain `IPropertyHandler<string, Guid>` usable with any operation, not just the bulk ones.
- `BulkMerge` uses a real, single-statement ANSI `MERGE` when `identityBehavior` is `KeepIdentity`. When `ReturnIdentity` is requested, a three-step sequence runs instead: (1) copy the existing identity value onto every pseudo row that already matches a target row, (2) assign a fresh, gap-free identity — via a correlated `COUNT(*)`, deliberately plain portable ANSI SQL rather than a window function — to every unmatched row, then (3) run the `MERGE` with the identity column inserted explicitly. See [BulkMerge](/operation/saphana/bulkmerge#identity-setting-alignment) for details.
- `BulkUpdate`/the update half of `BulkMerge` uses a correlated-subquery `SET (col1, col2, ...) = (SELECT ...)` form guarded by `WHERE EXISTS`, since SAP HANA has no multi-table `UPDATE ... JOIN`.
- Dropping a pseudo table swallows SAP HANA's "invalid table name" native error (`259`) — HANA's `DROP TABLE` has no `IF EXISTS` clause, so a drop against a pseudo table that was never created (or already dropped) is expected to fail this specific way, and only this way.

### Known limitations (v1)

- **Pseudo table names are deterministic, not per-call-unique** — built from the target table name, the operation, and the `pseudoTableType` (e.g. `PhysicalPersonMerge`). Combined with the next limitation, this means two concurrent bulk calls of the *same operation* against the *same table* can genuinely interfere with each other's staged rows.
- **`Auto` and `Memory` both currently resolve to `Physical`** at runtime — the internal resolution logic returns `Physical` on every outcome, so the session-isolated `LOCAL TEMPORARY` path `Memory` advertises is not implemented yet. Avoid running concurrent SAP HANA bulk operations of the same kind against the same table until this is resolved.
- Every "bulk" write is really a client-buffered loop of single-row `INSERT` statements — one round trip per row. `batchSize` (default `500`) only controls how many rows are buffered between flushes; it does not reduce round trips.
- `BulkInsert`/`BulkMerge`'s `ReturnIdentity` identity pre-assignment reads the live `MAX(identity)` off the table's own row data (not a cached sequence counter), which can never be stale but does leave a small race window against a concurrent writer to the same table between that read and the final `INSERT`/`MERGE`.
- Per the library's own source comments, `BulkMerge`'s correlated-`COUNT` rank computation for assigning fresh identities to unmatched rows in the `ReturnIdentity` path is **the least-verified statement in the whole provider**. Verify it specifically, especially under concurrent writers, before relying on it in production.
- The `DbDataReader` overloads of `BulkInsert` and `BulkMerge` have no `identityBehavior` argument — a forward-only, single-pass reader cannot be rewound to correlate generated identity values back onto a source row.
- `BulkDeleteByKey` takes a `tableName` string directly (`BulkDeleteByKey<TPrimaryKey>(connection, tableName, primaryKeys, ...)`) rather than an entity type parameter.
- This package inherits every [Known limitation of RepoDb.SapHana](/release/saphana) itself (e.g. no table hints, `Merge` requiring a known primary key value up front).

- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `RepoDb.SapHana` package `v0.0.1-alpha`.
- Referenced the `Sap.Data.Hana.Net.v6.0` package `v2.29.25`.
