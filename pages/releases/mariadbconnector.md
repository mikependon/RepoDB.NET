---
layout: default
sidebar: releases
title: MariaDB (MariaDbConnector)
description: "This page contains the latest information of the releases of RepoDb.MariaDbConnector library."
nav_order: 18
permalink: /release/mariadbconnector
parent: RELEASES
---

# Releases for RepoDb.MariaDbConnector

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.MariaDbConnector) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.MariaDbConnector).

## RepoDb.MariaDbConnector (v0.0.1-alpha1) - Preview

Released: TBA

New
{: .label .label-green }

First (alpha) release of the dedicated MariaDB provider for RepoDB, built on top of the [MySqlConnector](https://www.nuget.org/packages/MySqlConnector)-based [RepoDb.Connector.MariaDbConnector](https://www.nuget.org/packages/RepoDb.Connector.MariaDbConnector) — a thin ADO.NET wrapper and type-mapping layer that exposes MariaDB-specific, `MariaDb`-prefixed objects (`MariaDbConnection`, `MariaDbParameter`, `MariaDbType`, etc.) so this package never collides with [RepoDb.MySqlConnector](/release/mysqlconnector). [#1270](https://github.com/mikependon/RepoDb/issues/1270) [#1271](https://github.com/mikependon/RepoDb/issues/1271)

> This package started as a direct copy of [RepoDb.MySqlConnector](/release/mysqlconnector), since MariaDB is largely wire- and SQL-compatible with MySQL. It is published as its own dedicated package rather than folded into RepoDb.MySqlConnector so that MariaDB support can be versioned, tuned, and evolved independently. It is the `MySqlConnector`-based counterpart to [RepoDb.MariaDb](/release/mariadb), which is built on `MySql.Data` instead — pick whichever underlying driver best fits your project.

### What's included

- `MariaDbBootstrap` / `MariaDbGlobalConfiguration.UseMariaDb()` — initializes RepoDB for use with `MariaDbConnection` (`GlobalConfiguration.Setup().UseMariaDb()`), mapping the `MariaDbDbSetting`, `MariaDbDbHelper`, and `MariaDbStatementBuilder` to every `MariaDbConnection`.
- `MariaDbDbSetting` — MariaDB-specific behavior: backtick (`` ` ``) opening/closing identifier quotes, `@` parameter prefix, no table hints, no native `UPSERT` keyword (`Merge` is built via `INSERT ... ON DUPLICATE KEY UPDATE` instead), and multi-statement command text support (`IsMultiStatementExecutable = true`, `MultiStatementSeparator = ";"`).
- `MariaDbStatementBuilder` — generates MariaDB-flavored SQL for every operation: `LIMIT`/`LIMIT ... OFFSET` paging for [BatchQuery](/operation/batchquery)/skip-take queries and top-N [Query](/operation/query), `INSERT ... ON DUPLICATE KEY UPDATE` for [Merge](/operation/merge)/[MergeAll](/operation/mergeall), and identity/primary-key retrieval via `LAST_INSERT_ID()` for [Insert](/operation/insert)/[InsertAll](/operation/insertall)/[Merge](/operation/merge)/[MergeAll](/operation/mergeall).
- `MariaDbDbHelper` — schema/type discovery (`GetFields`/`GetFieldsAsync`) sourced from `INFORMATION_SCHEMA.COLUMNS`, and generated-identity retrieval (`GetScopeIdentity`/`GetScopeIdentityAsync`) via `SELECT LAST_INSERT_ID();`.
- Resolvers — `MariaDbDbTypeNameToClientTypeResolver` (maps `INFORMATION_SCHEMA.COLUMNS.DATA_TYPE` values to .NET CLR types) and `MariaDbDbTypeToStringNameResolver` (maps `System.Data.DbType` to MariaDB SQL type names).
- Parameter attribute mirroring `MariaDbParameter.MariaDbType`, settable per entity property: [MariaDbType](/attribute/mariadb/mariadbconnector/mariadbtype).
- [QueryMultiple](/operation/executequerymultiple)/`QueryMultipleAsync` run as a single combined, multi-statement command text (`"SELECT ... T1; SELECT ... T2"`), the same as MySQL/SQL Server/PostgreSQL/Db2 — no per-type round trip.
- Targets .NET Standard 2.0, .NET 8, .NET 9, and .NET 10.

### Known limitations (v1)

- Install only one of `RepoDb.MariaDbConnector` or [RepoDb.MariaDb](/release/mariadb) per project — both call the identically-named `MariaDbBootstrap.Initialize()`/`GlobalConfiguration.Setup().UseMariaDb()` and map against the identically-named `MariaDbConnection` type, so referencing both lets the second package's mappings silently overwrite the first's. See [Get Started for MariaDB](/tutorial/get-started-mariadb) for details.
- `RepoDb.Connector.MariaDbConnector` is an independent, unofficial ADO.NET provider for MariaDB (a thin wrapper over `MySqlConnector`) — not affiliated with or endorsed by MariaDB plc or the MariaDB Foundation, and still in early development.
- No native GUID/`UNIQUEIDENTIFIER` type — as with MySQL, map a `Guid` property as `string`/`byte[]`, or register a custom [IPropertyHandler](/interface/ipropertyhandler).

### Bumps

- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `RepoDb.Connector.MariaDbConnector` package `v0.0.1-alpha2`.
