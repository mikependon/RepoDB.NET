---
layout: default
sidebar: releases
title: MySqlConnector (Bulk)
description: "This page contains the latest information of the releases of RepoDb.MySqlConnector.BulkOperations library."
permalink: /release/mysqlconnectorbulk
parent: RELEASES
---

# Releases for RepoDb.MySqlConnector.BulkOperations

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.MySqlConnector.BulkOperations) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.MySqlConnector.BulkOperations).

## RepoDb.MySqlConnector.BulkOperations (v0.0.1-alpha1)

Released: August 81, 2026

New
{: .label .label-green }

First release of the bulk operations extension for [RepoDb.MySqlConnector](/release/mysqlconnector), providing `BulkInsert`, `BulkMerge`, `BulkUpdate`, `BulkDelete`, and `BulkDeleteByKey`, each with an `Async` overload, callable against a `MySqlConnectorConnection`, a table name, or a `DataTable`.

> **Verification status:** this package has been implemented and reviewed, but not yet exercised against a live MySqlConnector instance. Verify the bulk-load path, the identity read-back, and the staging-table strategy end-to-end before relying on this package in production.

### What's included

- [BulkInsert](/operation/sqlserver/bulkinsert), [BulkMerge](/operation/sqlserver/bulkmerge), and [BulkUpdate](/operation/sqlserver/bulkupdate).
- [BulkDelete](/operation/sqlserver/bulkdelete) — matched by qualifiers, staging the qualifier columns only.
- [BulkDeleteByKey](/operation/sqlserver/bulkdeletebykey) — matched by primary key, as a dedicated overload separate from `BulkDelete`.
- `MySqlConnectorBulkImportIdentityBehavior` (`Unspecified`, `KeepIdentity`, `ReturnIdentity`) — controls whether identity values are sent, ignored, or read back.
- `MySqlConnectorBulkImportPseudoTableType` (`Auto`, `Memory`, `Physical`) — picks the staging-table strategy used by `BulkMerge`/`BulkUpdate`/`BulkDelete`.
- `MySqlConnectorBulkInsertMapItem` — explicit source-to-destination column mapping for `BulkInsert`.

### Known limitations (v1)

- The bulk-load step is agnostic of the caller's transaction — rows it writes commit independently, so a rolled-back transaction will not undo a plain `BulkInsert`'s already-copied rows. Request `ReturnIdentity` to force the transactional array-bind path if this matters for your workload.
- `Memory` staging currently always resolves to `Physical`, which has no per-session isolation — concurrent callers bulk-writing to the same table will race each other's staged data.
- The staging table for a given (table name, pseudo table type) combination is created once per process and reused; the first call against it commits any pending work in its transaction.

- Referenced the `RepoDb` package `v1.16.0-alpha2`.
- Referenced the `RepoDb.MySqlConnector` package `v1.16.0-alpha1`.
