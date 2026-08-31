---
layout: default
sidebar: releases
title: Vertica (Bulk)
description: "This page contains the latest information of the releases of RepoDb.Vertica.BulkOperations library."
permalink: /release/verticabulk
parent: RELEASES
---

# Releases for RepoDb.Vertica.BulkOperations

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.Vertica.BulkOperations) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.Vertica.BulkOperations).

## RepoDb.Vertica.BulkOperations (v0.0.1-alpha) - Preview

Released: 2026-08-31

New
{: .label .label-green }

First release of the bulk operations extension for [RepoDb.Vertica](/release/vertica), built on `VerticaCopyStream` — `Vertica.Data`'s native `COPY ... FROM STDIN` streaming API.

> **Verification status:** this package has been implemented and reviewed, but not yet exercised against a live Vertica instance. Verify the pseudo-table lifecycle, the `COPY` stream's value formatting (dates, booleans, binary), and the identity-read-back arithmetic end-to-end before relying on this package in production.

### What's included

- [BulkInsert](/operation/vertica/bulkinsert), [BulkMerge](/operation/vertica/bulkmerge), [BulkUpdate](/operation/vertica/bulkupdate), [BulkDelete](/operation/vertica/bulkdelete) and [BulkDeleteByKey](/operation/vertica/bulkdeletebykey), each with an `Async` overload, callable against a `VerticaConnection` with an entity list, a `DataTable`, or an `IDataReader` (`BulkDeleteByKey` takes a list of primary key values instead).
- An internal `COPY`-stream-based bulk-copy implementation, used to write into the target table (`BulkInsert` without `ReturnIdentity`) and into the pseudo (staging) table backing the other operations. It formats each row as a tab-delimited, newline-terminated record (backslash-escaping literal backslashes/tabs/CR/LF), with Postgres-lineage `t`/`f` boolean literals and hex-encoded binary values, and runs synchronously on the calling thread — the async overloads offload that synchronous sequence to a background thread instead, since `VerticaCopyStream` exposes no async API of its own.
- `VerticaBulkImportIdentityBehavior` (`KeepIdentity` default, `ReturnIdentity`) — controls whether identity values are sent as-is or read back. `ReturnIdentity` reads generated values back via a single `SELECT LAST_INSERT_ID()`, back-computing every row's individual identity from Vertica's contiguous, insertion-ordered `IDENTITY`/`AUTO_INCREMENT` assignment — no per-row round trip needed.
- `VerticaBulkImportPseudoTableType` (`Auto`, `Memory`, `Physical`) — selects the staging-table strategy backing `BulkMerge`, `BulkUpdate`, `BulkDelete`, `BulkDeleteByKey`, and `BulkInsert` (when `ReturnIdentity` is used). Every staging table is created with a per-call unique name, so `Memory` and `Physical` are both genuinely functional and safe under concurrent callers writing against the same target table.
- [VerticaBulkInsertMapItem](/class/vertica/verticabulkinsertmapitem) — explicit source-to-destination column mapping. Its optional `VerticaType` override is not currently consumed by the `COPY`-based implementation (Vertica's `COPY` parser infers wire format from the destination column's own server-side type) — kept only as a forward-looking escape hatch, matching every other bulk-operations package's map-item shape.
- [VerticaTraceKeys](/class/vertica/verticatracekeys) — the tracing key constants for the five bulk operations, for use with [ITrace](/interface/itrace).
- `BulkMerge` never generates a native `MERGE` statement (Vertica rejects it outright against a table with an `IDENTITY`/`AUTO_INCREMENT` column) — it always issues a separate `UPDATE ... FROM` followed by a separate `INSERT ... WHERE NOT EXISTS (...)`, as two distinct round trips rather than one compound statement. See [BulkMerge](/operation/vertica/bulkmerge#operation-sql-statements) for the full mechanics.

### Known limitations (v1)

- `BulkCopyTimeout` is accepted on every operation for signature symmetry with the other providers' bulk-operations packages, but `VerticaCopyStream` has no timeout-equivalent property to apply it to — the argument currently has no effect.
- Identity read-back (for both `BulkInsert` and `BulkMerge`) relies on `VerticaDbHelper.GetScopeIdentity`'s `SELECT LAST_INSERT_ID()` query plus a descending-offset back-computation that assumes Vertica assigns `IDENTITY`/`AUTO_INCREMENT` values strictly contiguously in insertion order. Neither the underlying query nor this assumption has been verified against a live Vertica instance; verify carefully under concurrent writers before relying on it in production.
- The `DbDataReader` overloads of `BulkInsert` and `BulkMerge` have no `identityBehavior` argument — a forward-only, single-pass reader cannot be rewound to correlate generated identity values back onto a source row.
- `BulkUpdate` (and the update half of `BulkMerge`) skip the generated `UPDATE` statement entirely when every staged field is also a qualifier (i.e. there is nothing left to actually update).
- The synchronous `WriteToServer`/`Execute` path is not thread-safe for concurrent use against the same connection — the pseudo table is created, indexed, read from, and dropped via other statements against that same connection immediately before/after the copy, so the copy itself must run on whatever thread is already driving that sequence. The async overloads offload to a background thread only because nothing else touches the connection concurrently while they await.
- Every bulk call against a table it hasn't seen before creates its own uniquely-named staging table (`CREATE TABLE`/`CREATE GLOBAL TEMPORARY TABLE`) and drops it once the call completes, rather than creating one per (table, pseudo table type) and reusing it across calls the way the Oracle bulk package does.
- This package inherits every [Known limitation of RepoDb.Vertica](/release/vertica) itself (e.g. no table hints, `MaxParameterCount` of `1500`, the unverified `Merge`/`MergeAll` compound-statement behavior).

- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `RepoDb.Vertica` package `v0.0.1-alpha`.
- Referenced the `Vertica.Data` package `v24.3.0`.
