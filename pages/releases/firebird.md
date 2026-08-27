---
layout: default
sidebar: releases
title: Firebird
description: "This page contains the latest information of the releases of RepoDb.Firebird library."
permalink: /release/firebird
parent: RELEASES
---

# Releases for RepoDb.Firebird

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.Firebird) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.Firebird).

## RepoDb.Firebird (v0.0.1-alpha) - Preview

Released: TBA

New
{: .label .label-green }

First (alpha) release of the Firebird provider for RepoDB, built on top of [FirebirdSql.Data.FirebirdClient](https://www.nuget.org/packages/FirebirdSql.Data.FirebirdClient). Targets Firebird 3.0 and later, and netstandard2.0, .NET 8, .NET 9, and .NET 10.

### What's included

- [FirebirdBootstrap](/class/firebird/firebirdbootstrap) / [FirebirdConfiguration](/class/firebird/firebirdconfiguration)`.UseFirebird()` — initializes RepoDB for use with `FbConnection` (`GlobalConfiguration.Setup().UseFirebird()`), mapping the [FirebirdDbSetting](/class/firebird/firebirddbsetting), [FirebirdDbHelper](/class/firebird/firebirddbhelper), and [FirebirdStatementBuilder](/class/firebird/firebirdstatementbuilder) to every `FbConnection`.
- [FirebirdDbSetting](/class/firebird/firebirddbsetting) — Firebird-specific behavior: `"` for opening/closing identifier quotes, `@` parameter prefix, no table hints, no multi-statement command text support (`IsMultiStatementExecutable = false`), and a lower `MaxParameterCount` of `1500` (Firebird's DSQL parser rejects an `IN (...)` list past roughly 1500 members).
- [FirebirdStatementBuilder](/class/firebird/firebirdstatementbuilder) — generates Firebird-flavored SQL for every operation: `FIRST n`/`FIRST m SKIP n` paging for [Query](/operation/query)/[Exists](/operation/exists)/[BatchQuery](/operation/batchquery)/skip-take queries, a native `RETURNING` clause for identity/primary-key retrieval on [Insert](/operation/insert), `UPDATE OR INSERT INTO ... MATCHING (...) RETURNING ...` (Firebird's native upsert) for [Merge](/operation/merge)/[MergeAll](/operation/mergeall) — falling back to an `EXECUTE BLOCK` PL/SQL construct when the identity column is itself a qualifier — and a plain `DELETE FROM t` for [Truncate](/operation/truncate), since Firebird has no `TRUNCATE TABLE` statement.
- [FirebirdDbHelper](/class/firebird/firebirddbhelper) — schema/type discovery (`GetFields`/`GetFieldsAsync`) sourced from `RDB$RELATION_FIELDS`/`RDB$FIELDS`, with identity-column detection via `RDB$IDENTITY_TYPE` (Firebird 3.0+ only). `GetScopeIdentity`/`GetScopeIdentityAsync` always throw `NotSupportedException`, since Firebird has no session-wide "last identity" construct.
- Resolvers — [FirebirdConvertFieldResolver](/class/firebird/firebirdconvertfieldresolver) (casts a field to its Firebird type when a conversion is needed, e.g. typed [ExecuteQuery](/operation/executequery) results, and widens integer [Average](/operation/average)/[AverageAll](/operation/averageall) casts to `DOUBLE PRECISION` since Firebird's `AVG()` otherwise truncates), [FirebirdDbTypeNameToClientTypeResolver](/class/firebird/firebirddbtypenametoclienttyperesolver) (maps the canonical type-name strings produced by [FirebirdDbHelper](/class/firebird/firebirddbhelper) to .NET CLR types), and [DbTypeToFirebirdStringNameResolver](/class/firebird/dbtypetofirebirdstringnameresolver) (maps `System.Data.DbType` to Firebird SQL type names, e.g. `DbType.Guid` → `CHAR(16) CHARACTER SET OCTETS`).
- Parameter attributes mirroring `FbParameter` members, settable per entity property: [Charset](/attribute/firebird/charset), [FbDbType](/attribute/firebird/fbdbtype), [SourceColumn](/attribute/firebird/sourcecolumn), [SourceColumnNullMapping](/attribute/firebird/sourcecolumnnullmapping), and [SourceVersion](/attribute/firebird/sourceversion).

### Known limitations (v1)

- [InsertAll](/operation/insertall), [MergeAll](/operation/mergeall) and [UpdateAll](/operation/updateall) issue a separate round trip per row rather than a single batched statement, since `IsMultiStatementExecutable` is `false`. Passing an explicit `batchSize` greater than `1` to any of them throws a `NotSupportedException`. Use the `BulkInsert`/`BulkMerge`/`BulkUpdate` operations from [RepoDb.Firebird.BulkOperations](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations) for genuine multi-row batching.
- Firebird 2.5 and earlier are not supported. Identity-column introspection relies on `RDB$RELATION_FIELDS.RDB$IDENTITY_TYPE`/`RDB$GENERATOR_NAME`, which do not exist prior to Firebird 3.0 — a table whose auto-increment behavior is implemented the pre-3.0 way (a `BEFORE INSERT` trigger plus a bare generator/sequence) is not detected as an identity column.
- No table hints of any kind — `AreTableHintsSupported` is `false`; passing a non-null `hints` argument to any operation throws a `NotSupportedException`.
- No session-wide scope identity — [GetScopeIdentity](/class/firebird/firebirddbhelper)/`GetScopeIdentityAsync` always throw `NotSupportedException`. Use the value already returned by [Insert](/operation/insert)/[Merge](/operation/merge) via `RETURNING`, or query the underlying generator explicitly (e.g. `GEN_ID(generator_name, 0)`) if you need it out-of-band.
- No `TRUNCATE TABLE` statement (as of Firebird 5.0) — [Truncate](/operation/truncate) compiles to `DELETE FROM t` without a `WHERE` clause, which, unlike a real truncate, does not reset a `GENERATED ... AS IDENTITY` column's next value.
- `MaxParameterCount` defaults to `1500`, lower than the `2098` most other providers default to, since Firebird's DSQL parser rejects an `IN (...)` list past roughly 1500 members with `"Implementation limit exceeded"`.
- [Merge](/operation/merge)/[MergeAll](/operation/mergeall) compile to a single plain `UPDATE OR INSERT` statement only when the identity column (if any) is not itself a qualifier; when it is, an `EXECUTE BLOCK` PL/SQL construct is used instead, since `MATCHING` cannot reliably match a not-yet-inserted row against its own not-yet-known identity value.
- No native GUID type — a `Guid` entity property maps to `CHAR(16) CHARACTER SET OCTETS`; there is currently no dedicated property handler equivalent to other providers' `GuidToByteArrayPropertyHandler`.
- Only a standard networked Firebird 3.0+ server (SuperServer) accessed via `FirebirdSql.Data.FirebirdClient` is tested against — embedded (`fbembed`) and Classic/SuperClassic server modes are not specifically verified.

- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `FirebirdSql.Data.FirebirdClient` package `v10.3.4`.
