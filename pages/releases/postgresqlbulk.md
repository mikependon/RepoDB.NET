---
layout: default
sidebar: releases
title: PostgreSQL (Bulk)
description: "This page contains the latest information of the releases of RepoDb.PostgreSql.BulkOperations library."
nav_order: 5
permalink: /release/postgresqlbulk
parent: RELEASES
---

# Releases for RepoDb.PostgreSql.BulkOperations

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.PostgreSql.BulkOperations).

## RepoDb.PostgreSql.BulkOperations (v1.15.0)

- Referenced the `RepoDb` package `v1.15.0`.
- Referenced the `RepoDb.PostgreSql` package `v1.15.0`.


## RepoDb.PostgreSql.BulkOperations (v1.14.0)

New
{: .label .label-green }

- Added `DataTypeName` property to `NpgsqlBulkInsertMapItem` — enables explicit PostgreSQL type name mapping (e.g. native enum types) as a third constructor overload. When set, `DataTypeName` takes precedence over `NpgsqlDbType`.
- Updated `Compiler.cs` to resolve `NpgsqlBinaryImporter.Write` / `WriteAsync` overloads that accept a `string dataTypeName` parameter — covers both sync and async paths.
- Fixed all 64 `EnumTest` integration test failures — [BinaryBulkInsert](/operation/binarybulkinsert), [BinaryBulkDelete](/operation/binarybulkdelete), [BinaryBulkMerge](/operation/binarybulkmerge), and [BinaryBulkUpdate](/operation/binarybulkupdate) now correctly handle .NET enums mapped to native PostgreSQL enum types via `NpgsqlDataSource.MapEnum()`.
- Added support for .NET 8, .NET 9, and .NET 10 target frameworks.
- Referenced the `RepoDb` package `v1.14.0`.
- Referenced the `RepoDb.PostgreSql` package `v1.14.0`.

Breaking
{: .label .label-red }

- Dropped support for .NET 6 and .NET 7.


## RepoDb.PostgreSql.BulkOperations (v1.13.1)

- Fixed the issues when writing the records into the database via `System.DataTable` object using the [BinaryBulkImport](/operation/binaryimport), when an Enum-based property is present.
- Referenced the `RepoDb` package `v1.13.1`.
- Referenced the `RepoDb.PostgreSql` package `v1.13.1`.


## RepoDb.PostgreSql.BulkOperations (v1.13.1-alpha1)

- Enabled the `net7.0` TFM.


## RepoDB.PostgreSql.BulkOperations (v1.13.0)

- Referenced the `RepoDb` package `v1.13.0`.
- Referenced the `RepoDb.PostgreSql` package `v1.13.0`.


## RepoDB.PostgreSql.BulkOperations (v0.0.12)

- Referenced the `RepoDb` package `v1.13.0-beta1`.
- Referenced the `RepoDb.PostgreSql` package `v1.13.0-beta1`.


## RepoDB.PostgreSql.BulkOperations (v0.0.11)

- Referenced the `RepoDb` package `v1.13.0-alpha2`.
- Referenced the `RepoDb.PostgreSql` package `v1.13.0-alpha2`.


## RepoDB.PostgreSql.BulkOperations (v0.0.10)

- Referenced the `RepoDb` package `v1.13.0-alpha1`.
- Referenced the `RepoDb.PostgreSql` package `v1.13.0-alpha1`.
- Upgraded the `Npgsql` package to `v6.0.1`.


## RepoDB.PostgreSql.BulkOperations (v0.0.9)

- Referenced the `RepoDb` package `v1.12.10`.
- Upgraded the `RepoDb.PostgreSql` package to `v1.1.5`.


## RepoDB.PostgreSql.BulkOperations (v0.0.8)

- Added enum support across all bulk operations for all entity types (`TEntity`, [IDictionary<string, object>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.idictionary-2?view=net-6.0), [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0), `Anonymous`, and `DataTable`).
- Supported the `.NET v6.0`.
- Added the `Title` and `PackageTags` tags in the Nuget Package (.nuspec references).
- Upgraded the `RepoDb` package to `v1.12.10-beta4`.
- Upgraded the `RepoDb.PostgreSql` package to `v1.1.5-beta2`.


## RepoDB.PostgreSql.BulkOperations (v0.0.7)

- Added the bulk operations extended methods on the [DbRepository](/class/dbrepository) class.
- Added the bulk operations extended methods on the [BaseRepository](/class/baserepository) class.


## RepoDB.PostgreSql.BulkOperations (v0.0.6)

- Introduced the [BinaryBulkDeleteByKey](/operation/binarybulkdeletebykey) operation.


## RepoDB.PostgreSql.BulkOperations (v0.0.5)

- Introduced the [BinaryBulkDelete](/operation/binarybulkdelete) operation.


## RepoDB.PostgreSql.BulkOperations (v0.0.4)

- Introduced the [BinaryBulkUpdate](/operation/binarybulkupdate) operation.


## RepoDB.PostgreSql.BulkOperations (v0.0.3)

- Introduced the [BinaryBulkMerge](/operation/binarybulkmerge) operation.


## RepoDB.PostgreSql.BulkOperations (v0.0.2)

- Introduced the [BinaryBulkInsert](/operation/binarybulkinsert) operation.


## RepoDB.PostgreSql.BulkOperations (v0.0.1)

- Initial release of the [RepoDb.PostgreSql.BulkOperations](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations/0.0.1) package.
- Introduced the [BinaryImport](/operation/binaryimport) operation.