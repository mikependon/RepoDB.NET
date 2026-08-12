---
layout: default
sidebar: releases
title: Db2 (Bulk)
description: "This page contains the latest information of the releases of RepoDb.Db2.BulkOperations library."
nav_order: 13
permalink: /release/db2bulk
parent: RELEASES
---

# Releases for RepoDb.Db2.BulkOperations

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.Db2.BulkOperations) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.Db2.BulkOperations).

## RepoDb.Db2.BulkOperations (v0.0.1-alpha1) - Preview

Released: TBA

New
{: .label .label-green }

First release of the bulk operations extension for [RepoDb.Db2](/release/db2), built on IBM's Data Server .NET Provider's `DB2BulkCopy` and Db2's `FINAL TABLE` insert clause for identity read-back.

> **Verification status:** this package has been implemented and reviewed; the entity-to-`DataTable`/property-handler path has been spot-checked against a live Db2 LUW instance (see the `Guid`/`CHAR(n) FOR BIT DATA` note below), but the package has not been fully exercised end-to-end. Verify the staging-table lifecycle, the `FINAL TABLE` identity read-back ordering, and the multi-step `BulkMerge` correlation before relying on this package in production.

### What's included

- [BulkInsert](/operation/sqlserver/bulkinsert), [BulkMerge](/operation/sqlserver/bulkmerge), [BulkUpdate](/operation/sqlserver/bulkupdate), [BulkDelete](/operation/sqlserver/bulkdelete), and [BulkDeleteByKey](/operation/sqlserver/bulkdeletebykey), each with an `Async` overload, callable against a `DB2Connection` with an entity list, a `DataTable`, or an `IDataReader` (`BulkDeleteByKey` takes a list of primary key values instead).
- `Db2BulkImportIdentityBehavior` (`KeepIdentity` default, `ReturnIdentity`) - controls whether identity values are sent as-is or read back. `ReturnIdentity` reads the generated values back via Db2's `SELECT ... FROM FINAL TABLE (INSERT ...)` clause on the same statement that loads from the staging table into the real one - no separate array-bind round trip is needed the way Oracle's `OracleBulkCopy` requires.
- `Db2BulkImportPseudoTableType` (`Auto`, `Memory`, `Physical`) - selects the staging-table strategy backing `BulkInsert` (when `ReturnIdentity` is used), `BulkMerge`, `BulkUpdate`, `BulkDelete`, and `BulkDeleteByKey`.
- `Db2BulkInsertMapItem` - explicit source-to-destination column mapping for `BulkInsert`, with an optional `DB2Type` override.
- Entities are staged through an in-memory `DataTable` rather than streamed directly through a data reader, so property handlers run before the bulk copy - confirmed live against Db2 LUW to be required for a `Guid`-backed `CHAR(n) FOR BIT DATA` column, which silently breaks if streamed without going through a handler.
- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `RepoDb.Db2` package `v0.0.1-alpha`.
- Referenced the `Net.IBM.Data.Db2` / `Net.IBM.Data.Db2-lnx` package `v9.0.0.400` (`v10.0.0.200` on .NET 10).

### Known limitations (v1)

- `Db2BulkImportPseudoTableType.Auto` and `Memory` both currently resolve to `Physical` regardless of row count - the internal auto-resolution logic returns `Physical` on every outcome, so there is no session-private staging path implemented yet despite the enum advertising one.
- Every bulk call creates its own physical staging table (`CREATE TABLE ... DEFINITION ONLY`) and drops it once the call completes, rather than creating one per (table, pseudo table type) and reusing it across calls the way the Oracle bulk package does. Since `CREATE TABLE`/`DROP TABLE` are DDL and commonly force a commit boundary, this happens on every single call, not just the first.
- The `DB2BulkCopy` load into the staging table is constructed without the `transaction` parameter accepted by the operation, so that portion of the write does not participate in the caller-supplied transaction - only the surrounding staging-table DDL and the final `INSERT`/`MERGE`/`UPDATE`/`DELETE` against the real table do.
- `BulkMerge` with `ReturnIdentity` does not use a single atomic `MERGE`: it snapshots matched/unmatched rows with a `LEFT JOIN`, issues a separate `MERGE ... WHEN MATCHED` for the updates, then a separate `MERGE ... WHEN NOT MATCHED ... FINAL TABLE` insert to read back the new identities - three round trips instead of one, and the classification can go stale if another connection modifies the target table between the snapshot and the follow-up statements.
- `BulkInsert` with `ReturnIdentity` (and `BulkMerge`'s insert-only step) correlates generated identities back to source rows by sorting the `FINAL TABLE` result by the new identity value ascending and assuming that order matches the source row order - the same unverified ordering assumption already called out for `InsertAll` on the [core Db2 provider](/release/db2); this has not been verified against a live Db2 instance.
- No `Unspecified` identity-behavior state - unlike the Oracle bulk package's three-value enum, `Db2BulkImportIdentityBehavior` defaults straight to `KeepIdentity`.
