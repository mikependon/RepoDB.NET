---
layout: default
sidebar: releases
title: Oracle
description: "This page contains the latest information of the releases of RepoDb.Oracle library."
nav_order: 4
permalink: /release/oracle
parent: RELEASES
---

# Releases for RepoDb.Oracle

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.Oracle) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.Oracle).

## RepoDb.Oracle (v0.0.1-beta1) - Preview

Released: August 1, 2026

New
{: .label .label-green }

First release of the Oracle Database provider for RepoDB, built on top of [ODP.NET (Oracle.ManagedDataAccess.Core)](https://www.nuget.org/packages/Oracle.ManagedDataAccess.Core). Targets Oracle Database 12c and later, and .NET 8, .NET 9, and .NET 10.

### What's included

- `OracleBootstrap` / `OracleGlobalConfiguration.UseOracle()` — initializes RepoDB for use with `OracleConnection` (`GlobalConfiguration.Setup().UseOracle()`).
- `OracleDbSetting` — Oracle-specific behavior: `"` identifier quoting, `:` parameter prefix, no table hints, no native upsert, one statement per command.
- `OracleStatementBuilder` — generates Oracle SQL, including `OFFSET/FETCH` paging and identity retrieval via an implicit result set (`DBMS_SQL.RETURN_RESULT`).
- `OracleDbHelper` — schema and type discovery used for direct (non-repository) access.
- Resolvers (`OracleConvertFieldResolver`, `OracleDbTypeNameToClientTypeResolver`, `DbTypeToOracleStringNameResolver`) — map between .NET, Oracle, and `OracleDbType` types.
- `OracleGuidToByteArrayPropertyHandler` — optional property handler that maps a `Guid` to/from `RAW(16)`, since Oracle has no native GUID type.
- Parameter attributes mirroring `OracleParameter` members: `OracleDbTypeAttribute`, `OracleDbTypeExAttribute`, `ArrayBindSizeAttribute`, `ArrayBindStatusAttribute`, `CollectionTypeAttribute`, `OffsetAttribute`, `SkipConversionToLocalTimeAttribute`, `SourceColumnAttribute`, `SourceColumnNullMappingAttribute`, `SourceVersionAttribute`, `StatusAttribute`, and `UdtTypeNameAttribute`.

### Known limitations (v1)

- [QueryMultiple](/operation/executequerymultiple) issues one round trip per requested type instead of a single combined command, since ODP.NET rejects command text containing more than one SQL statement.
- [InsertAll](/operation/insertall) / [MergeAll](/operation/mergeall) execute one row per round trip for now; batched multi-row execution will follow in a later release.
- Identity retrieval on `Insert`/`Merge` relies on an Oracle 12c+ implicit result set wrapped in an anonymous PL/SQL block — verify against your own Oracle instance before relying on it in production.
- A `RETURNING` clause on `MERGE` is only supported starting with Oracle Database 23ai; on 12c–21c, a `Merge` against a table with a primary/identity key cannot return the key value.
- `Guid` entity properties need an explicit `byte[]` mapping, or the `OracleGuidToByteArrayPropertyHandler` registered per-property, since ODP.NET does not accept a raw `Guid` value.

- Referenced the `RepoDb` package `v1.15.1`.
- Referenced the `Oracle.ManagedDataAccess.Core` package `v23.9.1`.
