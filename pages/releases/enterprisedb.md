---
layout: default
sidebar: releases
title: EnterpriseDB
description: "This page contains the latest information of the releases of RepoDb.EnterpriseDb library."
permalink: /release/enterprisedb
parent: RELEASES
---

# Releases for RepoDb.EnterpriseDb

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.EnterpriseDb) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.EnterpriseDb).

## RepoDb.EnterpriseDb (v0.0.1-alpha) - Preview

Released: TBA

New
{: .label .label-green }

First (alpha) release of the EnterpriseDB provider for RepoDB, built on top of the Npgsql-backed [RepoDb.Connector.EnterpriseDb](https://www.nuget.org/packages/RepoDb.Connector.EnterpriseDb) connector for EDB Postgres Advanced Server. Targets .NET 8 and .NET 10.

### What's included

- [EnterpriseDbBootstrap](/class/enterprisedb/enterprisedbbootstrap) / [EnterpriseDbGlobalConfiguration](/class/enterprisedb/enterprisedbglobalconfiguration)`.UseEnterpriseDb()` — initializes RepoDB for use with `EDBConnection` (`GlobalConfiguration.Setup().UseEnterpriseDb()`), mapping the [EnterpriseDbDbSetting](/class/enterprisedb/enterprisedbdbsetting), [EnterpriseDbDbHelper](/class/enterprisedb/enterprisedbdbhelper), and [EnterpriseDbStatementBuilder](/class/enterprisedb/enterprisedbstatementbuilder) against `RepoDb.Connector.EnterpriseDb.EDBConnection`.
- [EnterpriseDbDbSetting](/class/enterprisedb/enterprisedbdbsetting) — `"` identifier quotes, `@` parameter prefix, `public` default schema, no table hints, multi-statement command text supported.
- [EnterpriseDbStatementBuilder](/class/enterprisedb/enterprisedbstatementbuilder) — generates EDB Postgres-flavored SQL: `LIMIT`/`LIMIT ... OFFSET` paging, a trailing `RETURNING` clause for [Insert](/operation/insert), `INSERT ... ON CONFLICT DO UPDATE` for [Merge](/operation/merge)/[MergeAll](/operation/mergeall) (with `OVERRIDING SYSTEM VALUE` for explicit identity inserts), and `RESTART IDENTITY` for [Truncate](/operation/truncate).
- [EnterpriseDbDbHelper](/class/enterprisedb/enterprisedbdbhelper) — schema/type discovery (`GetFields`/`GetFieldsAsync`) sourced from `information_schema.columns` joined against `pg_index`/`pg_namespace`. `GetScopeIdentity`/`GetScopeIdentityAsync` run `SELECT lastval();`. Both transparently retry once on a fresh connection if the original connection reports an operation already in progress. Array-valued parameters have their `EDBType` inferred directly by the driver; this helper only converts `DateOnly[]`/`TimeOnly[]` element arrays before binding.
- Resolvers — [EnterpriseDbDbTypeNameToClientTypeResolver](/class/enterprisedb/enterprisedbdbtypenametoclienttyperesolver) (database type name → .NET CLR type), [DbTypeToEnterpriseDbStringNameResolver](/class/enterprisedb/dbtypetoenterprisedbstringnameresolver) (`DbType` → EDB SQL type name), [ClientTypeToEDBDbTypeResolver](/class/enterprisedb/clienttypetoedbdbtyperesolver) (.NET CLR type → `EDBType`), and [EnterpriseDbDbTypeNameToEDBDbTypeResolver](/class/enterprisedb/enterprisedbdbtypenametoedbdbtyperesolver) (database type name → `EDBType`).
- [EnterpriseDbConvertFieldResolver](/class/enterprisedb/enterprisedbconvertfieldresolver) — builds `CAST(...)` expressions for typed [Field](/class/field) objects.
- Parameter attributes mirroring `EDBParameter` members, settable per entity property: [EnterpriseDbType](/attribute/enterprisedb/enterprisedbtype), [DbType](/attribute/enterprisedb/dbtype), [Direction](/attribute/enterprisedb/direction), [IsNullable](/attribute/enterprisedb/isnullable), [ParameterName](/attribute/enterprisedb/parametername), [Precision](/attribute/enterprisedb/precision), [Scale](/attribute/enterprisedb/scale), [Size](/attribute/enterprisedb/size), [SourceColumn](/attribute/enterprisedb/sourcecolumn), [SourceColumnNullMapping](/attribute/enterprisedb/sourcecolumnnullmapping), [SourceVersion](/attribute/enterprisedb/sourceversion), [Value](/attribute/enterprisedb/value), and the obsolete [ConvertedValue](/attribute/enterprisedb/convertedvalue).

- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `RepoDb.Connector.EnterpriseDb` package `v0.0.1-alpha2`.

### Known limitations (v1)

- [InsertAll](/operation/insertall) does not append a `RETURNING` clause (EDB Postgres rejects `RETURNING` against a sub-table `VALUES` expression) — generated identities are only returned via [Insert](/operation/insert) or the bulk operations in [RepoDb.EnterpriseDb.BulkOperations](/release/enterprisedbbulk).
- No table hints of any kind — `AreTableHintsSupported` is `false`; passing a non-null `hints` argument throws a `NotSupportedException`.
- The `[ConvertedValue]` attribute is obsolete — no longer supported by `RepoDb.Connector.EnterpriseDb`.
- No CLR representation exists for PostgreSQL geometric types (`box`, `circle`, `line`, `lseg`, `path`, `point`, `polygon`), `pg_lsn`, `tid`, or text-search types (`tsquery`, `tsvector`) on the Npgsql-backed connector this provider is built on — columns of these types resolve to `object` and have no matching `EDBType`.
- Only EDB Postgres Advanced Server is targeted; behavior against community PostgreSQL is not a design goal of this provider (use [RepoDb.PostgreSql](/release/postgresql) for that).
