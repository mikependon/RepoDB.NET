---
layout: default
sidebar: releases
title: Oracle (Bulk)
description: "This page contains the latest information of the releases of RepoDb.Oracle.BulkOperations library."
nav_order: 5
permalink: /release/oraclebulk
parent: RELEASES
---

# Releases for RepoDb.Oracle.BulkOperations

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.Oracle.BulkOperations) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.Oracle.BulkOperations).

## RepoDb.Oracle.BulkOperations (v0.0.1)

Released: August 1, 2026

New
{: .label .label-green }

First release of the bulk operations extension for [RepoDb.Oracle](/release/oracle), built on ODP.NET's `OracleBulkCopy` — the same genuine bulk-load primitive `SqlBulkCopy` is for SQL Server.

> **Verification status:** this package has been implemented and reviewed, but not yet exercised against a live Oracle instance. Verify the `OracleBulkCopy` load path, the array-bind identity read-back, and the staging-table strategy end-to-end before relying on this package in production.

### What's included

- [BulkInsert](/operation/sqlserver/bulkinsert), [BulkMerge](/operation/sqlserver/bulkmerge), [BulkUpdate](/operation/sqlserver/bulkupdate), and [BulkDelete](/operation/sqlserver/bulkdelete), each with an `Async` overload, callable against an `OracleConnection`, a table name, or a `DataTable`.
- `OracleBulkImportIdentityBehavior` (`Unspecified`, `KeepIdentity`, `ReturnIdentity`) — controls whether identity values are sent, ignored, or read back. `ReturnIdentity` switches `BulkInsert`'s row-load from `OracleBulkCopy` to array binding with `RETURNING ... INTO`, since `OracleBulkCopy` cannot report back generated values.
- `OracleBulkImportPseudoTableType` (`Auto`, `Memory`, `Physical`) — picks the staging-table strategy used by `BulkMerge`/`BulkUpdate`/`BulkDelete`: a Global Temporary Table, a shared heap table, or automatic selection by row count.
- `OracleBulkInsertMapItem` — explicit source-to-destination column mapping for `BulkInsert`, with an optional `OracleDbType` override.
- `BulkMerge` correlates returned identities back by qualifier after the `MERGE` completes, since Oracle's `RETURNING` clause on `MERGE` itself requires Oracle Database 23ai+.

### Known limitations (v1)

- `OracleBulkCopy` is agnostic of the caller's transaction — rows it writes commit independently, so a rolled-back transaction will not undo a plain `BulkInsert`'s already-copied rows. (`BulkMerge`/`BulkUpdate`/`BulkDelete` are unaffected, since only their staging-table load bypasses the transaction; the final `MERGE`/`UPDATE`/`DELETE` against the real table stays fully transactional.) Request `ReturnIdentity` to force the transactional array-bind path if this matters for your workload.
- `Memory` (Global Temporary Table) staging currently always resolves to `Physical`, since `OracleBulkCopy`'s direct-path load cannot write into a GTT. `Physical` staging tables have no per-session isolation — concurrent callers bulk-writing to the same table will race each other's staged data.
- The staging table for a given (table name, pseudo table type) combination is created once per process and reused; the first call against it commits any pending work in its transaction, since `CREATE TABLE`/`CREATE GLOBAL TEMPORARY TABLE` is DDL.

- Referenced the `RepoDb` package `v1.15.1`.
- Referenced the `RepoDb.Oracle` package `v0.0.1`.
- Referenced the `Oracle.ManagedDataAccess.Core` package `v23.9.1`.
