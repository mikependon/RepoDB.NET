---
layout: default
sidebar: releases
title: ClickHouse
description: "This page contains the latest information of the releases of RepoDb.ClickHouse library."
permalink: /release/clickhouse
parent: RELEASES
---

# Releases for RepoDb.ClickHouse

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.ClickHouse) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.ClickHouse).

## RepoDb.ClickHouse - Preview

Released: TBA

New
{: .label .label-green }

First release of the dedicated [ClickHouse](https://clickhouse.com/) provider for RepoDB, built on top of [ClickHouse.Driver](https://www.nuget.org/packages/ClickHouse.Driver). [#1242](https://github.com/mikependon/RepoDB/issues/1242)

### What's included

- `ClickHouseBootstrap` / `ClickHouseGlobalConfiguration.UseClickHouse()` — initializes RepoDB for use with `ClickHouseConnection` (`GlobalConfiguration.Setup().UseClickHouse()`), mapping the `ClickHouseDbSetting`, `ClickHouseDbHelper`, and `ClickHouseStatementBuilder` to every `ClickHouseConnection`.
- `ClickHouseConnection`, `ClickHouseCommand` and `ClickHouseTransaction` — RepoDB-aware subclasses of the driver's own ADO.NET objects. `ClickHouseCommand` transparently strips the leading `@` RepoDB adds to every parameter name, since `ClickHouse.Driver` expects unprefixed names. `ClickHouseTransaction`'s `Commit()`/`Rollback()` are no-ops, reflecting that ClickHouse has no traditional ACID transactions.
- `ClickHouseDbSetting` — ClickHouse-specific behavior: backtick (`` ` ``) opening/closing identifier quotes, `@` parameter prefix, no table hints, no native upsert keyword, and single-statement command text only (`IsMultiStatementExecutable = false`).
- `ClickHouseStatementBuilder` — generates ClickHouse-flavored SQL for every operation: `LIMIT`/`LIMIT ... OFFSET` paging for [BatchQuery](/operation/batchquery)/skip-take queries, `ALTER TABLE ... UPDATE`/`ALTER TABLE ... DELETE` mutations for [Update](/operation/update)/[UpdateAll](/operation/updateall)/[Delete](/operation/deleteall), and a plain `INSERT` for [Merge](/operation/merge)/[MergeAll](/operation/mergeall) (deduplication deferred to the table engine). [Insert](/operation/insert)/[InsertAll](/operation/insertall) throw `NotSupportedException` for entities with an identity field, since ClickHouse has no identity/auto-increment mechanism.
- `ClickHouseDbHelper` — schema/type discovery (`GetFields`/`GetFieldsAsync`) sourced from `system.columns`. `GetScopeIdentity`/`GetScopeIdentityAsync` always throw `NotSupportedException`.
- `ClickHouseDbTypeNameToClientTypeResolver` — maps ClickHouse column type names (unwrapping `Nullable(...)`/`LowCardinality(...)` and parameterized suffixes) to .NET CLR types.
- Parameter attributes: [ClickHouseType](/attribute/clickhouse/clickhousetype), [SourceColumn](/attribute/clickhouse/sourcecolumn), [SourceColumnNullMapping](/attribute/clickhouse/sourcecolumnnullmapping).
- Targets .NET 8, .NET 9, and .NET 10.

### Known limitations (v1)

- No identity/auto-increment/sequence of any kind — key values must always be supplied by the caller.
- No native `MERGE`/upsert statement — [Merge](/operation/merge)/[MergeAll](/operation/mergeall) compile to a plain `INSERT`; use [BulkMerge](/operation/clickhouse/bulkmerge) from [RepoDb.ClickHouse.BulkOperations](/release/clickhousebulk) for a real matched/unmatched merge.
- `UPDATE`/`DELETE` are asynchronous *mutations* (`ALTER TABLE ... UPDATE`/`DELETE`) applied by background merges, not immediate row-level changes, and the `WHERE` clause is mandatory.
- Transactions are accepted for API compatibility only — `Commit()`/`Rollback()` are no-ops.
- `Decimal` columns require `UseCustomDecimals=false` on the connection string; otherwise they are returned as the driver's own `ClickHouseDecimal` type instead of a plain `decimal`.
- Sub-second `DateTime64` precision requires an explicit [ClickHouseType](/attribute/clickhouse/clickhousetype) attribute on the property — a plain `DateTime` CLR parameter is otherwise inferred as whole-second precision.

### Bumps

- Referenced the `RepoDb` package.
- Referenced the `ClickHouse.Driver` package `v1.3.0`.
