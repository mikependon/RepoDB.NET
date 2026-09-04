---
layout: default
sidebar: releases
title: EnterpriseDB (Bulk)
description: "This page contains the latest information of the releases of RepoDb.EnterpriseDb.BulkOperations library."
permalink: /release/enterprisedbbulk
parent: RELEASES
---

# Releases for RepoDb.EnterpriseDb.BulkOperations

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.EnterpriseDb.BulkOperations) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.EnterpriseDb.BulkOperations).

## RepoDb.EnterpriseDb.BulkOperations (v0.0.1-alpha) - Preview

Released: TBA

New
{: .label .label-green }

First release of the bulk operations extension for [RepoDb.EnterpriseDb](/release/enterprisedb). Targets .NET 8, .NET 9, and .NET 10.

> **Architectural note:** rows are staged through a pseudo (staging) table and loaded via `RepoDb.Connector.EnterpriseDb`'s `EDBBulkCopy`, itself built on Npgsql's native binary `COPY` protocol — a genuine bulk load, not a row-by-row loop. `EDBConnection` throughout this package is `RepoDb.Connector.EnterpriseDb.EDBConnection`, not the official `EnterpriseDB.EDBClient` driver's connection type of the same name.

### What's included

- [BulkInsert](/operation/enterprisedb#bulkinsert), [BulkMerge](/operation/enterprisedb#bulkmerge), [BulkUpdate](/operation/enterprisedb#bulkupdate), [BulkDelete](/operation/enterprisedb#bulkdelete) and [BulkDeleteByKey](/operation/enterprisedb#bulkdeletebykey), each with an `Async` overload, callable against an `EDBConnection` with an entity list, a `DataTable`, or an `IDataReader` (`BulkDeleteByKey` takes a target table name and a list of primary key values directly, rather than an entity type parameter).
- `EDBBulkImportIdentityBehavior` (`KeepIdentity` default, `ReturnIdentity`) — controls whether identity values are sent as-is or read back after [BulkInsert](/operation/enterprisedb#bulkinsert)/[BulkMerge](/operation/enterprisedb#bulkmerge). `ReturnIdentity` for `BulkMerge` pre-assigns identities to unmatched rows via `nextval(pg_get_serial_sequence(...))` before an `ON CONFLICT DO UPDATE` upsert; for `BulkInsert` it inserts straight from the pseudo table with a `RETURNING` clause.
- `EDBBulkImportPseudoTableType` (`Auto`, `Memory`, `Physical`) — selects the staging-table strategy backing every operation; `Memory` creates a genuine session-scoped `TEMP` table, `Physical` an ordinary heap table under a deterministic name.
- [EDBBulkInsertMapItem](/class/enterprisedb/edbbulkinsertmapitem) — explicit source-to-destination column mapping, with an optional `EDBType` override. Explicit mappings are type-checked up front: an incompatible source/destination CLR type pairing (other than the allowed `Guid`↔`string` and integral-to-integral widenings) throws an `InvalidTypeException` immediately, rather than failing inside EnterpriseDB.
- [EDBTraceKeys](/class/enterprisedb/edbtracekeys) — the tracing key constants for the five bulk operations, for use with [ITrace](/interface/itrace).
- `BulkMerge` (`KeepIdentity`) uses a two-statement cascade — an `UPDATE ... FROM` for matched rows, then `INSERT ... WHERE NOT EXISTS (...)` for unmatched ones. `BulkMerge` (`ReturnIdentity`) instead pre-assigns identities and runs a single `INSERT ... ON CONFLICT DO UPDATE ... RETURNING`.
- `BulkUpdate` uses a correlated `UPDATE ... FROM "PseudoTable" S WHERE (...)` form. `BulkDelete`/`BulkDeleteByKey` use `DELETE ... USING "PseudoTable" S WHERE (...)`.
- Every pseudo table gets an identity `__RepoDbBulkRowOrder__` column recording each row's original load order, used to align generated identity values back onto the correct entity/`DataRow` after [BulkInsert](/operation/enterprisedb#bulkinsert)/[BulkMerge](/operation/enterprisedb#bulkmerge) with `ReturnIdentity`.
- Explicit column mappings are type-checked against the target table's [DbField](/class/dbfield) types before the write begins (`Guid`↔`string` and integral-to-integral widenings allowed), throwing `InvalidTypeException` up front on a mismatch.
- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `RepoDb.EnterpriseDb` package `v0.0.1-alpha`.
- Referenced the `RepoDb.Connector.EnterpriseDb` package `v0.0.1-alpha2`.

### Known limitations (v1)

- **Pseudo table names are deterministic, not per-call-unique** — built from the `pseudoTableType`, the target table name, and the operation (e.g. `PhysicalPersonMerge`). Two concurrent bulk calls of the *same operation* against the *same table*, both resolving to the same `pseudoTableType`, can interfere with each other's staged rows. Prefer `Memory` (a genuine per-session `TEMP` table) over `Physical` when running concurrent bulk operations from different connections against the same table.
- The `DbDataReader` overloads of `BulkInsert` and `BulkMerge` have no `identityBehavior` argument — a forward-only, single-pass reader cannot be rewound to correlate generated identity values back onto a source row.
- `BulkDeleteByKey` takes a `tableName` string directly (`BulkDeleteByKey<TPrimaryKey>(connection, tableName, primaryKeys, ...)`) rather than an entity type parameter.
- This package inherits every [Known limitation of RepoDb.EnterpriseDb](/release/enterprisedb) itself (e.g. no table hints).
