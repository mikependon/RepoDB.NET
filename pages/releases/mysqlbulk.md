---
layout: default
sidebar: releases
title: MySql (Bulk)
description: "This page contains the latest information of the releases of RepoDb.MySql.BulkOperations library."
nav_order: 9
permalink: /release/mysqlbulk
parent: RELEASES
---

# Releases for RepoDb.MySql.BulkOperations

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.MySql.BulkOperations) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.MySql.BulkOperations).

## RepoDb.MySql.BulkOperations (v0.0.1-alpha1)

Released: August 8, 2026

New
{: .label .label-green }

First release of the bulk operations extension for [RepoDb.MySql](/release/mysql), providing `BulkInsert`, `BulkMerge`, `BulkUpdate`, `BulkDelete`, and `BulkDeleteByKey`, each with an `Async` overload, callable against a `MySqlConnection` (`MySql.Data`), a table name, or a `DataTable`.

`MySql.Data` ships no reader-streaming bulk-copy API of its own, so every row-load in this package goes through a dedicated internal `MySqlBulkCopy` class — a `LOAD DATA LOCAL INFILE`-based stand-in, built on top of `MySql.Data`'s `MySqlBulkLoader`, that serializes rows to a temporary tab-delimited file before handing it off. It's the package's only row-load path, used by every operation including the identity read-back; there's no array-bind fallback and no dependency on the third-party `MySqlConnector` package or its own `MySqlBulkCopy` type.

> **Verification status:** this package has been implemented and reviewed, but not yet exercised against a live MySQL instance. Verify the bulk-load path, the identity read-back, and the staging-table strategy end-to-end before relying on this package in production.

### What's included

- `BulkInsert`, `BulkMerge`, and `BulkUpdate`.
- `BulkDelete` — matched by qualifiers, staging the qualifier columns only.
- `BulkDeleteByKey` — matched by primary key, as a dedicated overload separate from `BulkDelete`.
- `MySqlBulkImportIdentityBehavior` (`KeepIdentity`, `ReturnIdentity`) — controls whether identity values are left to `AUTO_INCREMENT` or pre-assigned and read back.
- `MySqlBulkImportPseudoTableType` (`Auto`, `Memory`, `Physical`) — picks the staging-table strategy used by `BulkMerge`/`BulkUpdate`/`BulkDelete`.
- `MySqlBulkInsertMapItem` — explicit source-to-destination column mapping for `BulkInsert`.

### Known limitations (v1)

- The internal `MySqlBulkCopy` issues `LOAD DATA LOCAL INFILE` directly against the connection, never through the caller's `MySqlTransaction` — a rolled-back transaction will not undo an already-loaded `BulkInsert`.
- `Memory` staging currently always resolves to `Physical`, which has no per-session isolation — concurrent callers bulk-writing to the same table will race each other's staged data.
- Every `BulkMerge`/`BulkUpdate`/`BulkDelete`/`BulkDeleteByKey` call recreates and drops its staging pseudo table, and each of those steps implicitly commits any other pending work on the connection (MySQL DDL auto-commits).
- Requires `AllowLoadLocalInfile=True;AllowUserVariables=True;` on the connection string, plus the server's `local_infile` global variable enabled.

- Referenced the `RepoDb` package `v1.16.0-alpha2`.
- Referenced the `RepoDb.MySql` package `v1.16.0-alpha1`.
