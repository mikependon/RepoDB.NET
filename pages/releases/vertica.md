---
layout: default
sidebar: releases
title: Vertica
description: "This page contains the latest information of the releases of RepoDb.Vertica library."
permalink: /release/vertica
parent: RELEASES
---

# Releases for RepoDb.Vertica

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.Vertica) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.Vertica).

## RepoDb.Vertica (v0.0.1-alpha) - Preview

Released: TBA

New
{: .label .label-green }

First (alpha) release of the Vertica provider for RepoDB, built on top of [Vertica.Data](https://www.nuget.org/packages/Vertica.Data). Targets netstandard2.0, .NET 8, .NET 9, and .NET 10.

> **Verification status:** this package has been implemented and reviewed, but not yet exercised against a live Vertica instance. Verify the Merge/MergeAll compound-statement behavior in particular (see Known limitations below) before relying on this package in production.

### What's included

- [VerticaBootstrap](/class/vertica/verticabootstrap) / [VerticaConfiguration](/class/vertica/verticaconfiguration)`.UseVertica()` — initializes RepoDB for use with `VerticaConnection` (`GlobalConfiguration.Setup().UseVertica()`), mapping the [VerticaDbSetting](/class/vertica/verticadbsetting), [VerticaDbHelper](/class/vertica/verticadbhelper), and [VerticaStatementBuilder](/class/vertica/verticastatementbuilder) to every `VerticaConnection`. Also forces `CultureInfo.CurrentCulture` to `CultureInfo.InvariantCulture` process-wide, working around `Vertica.Data` formatting date-like values using the ambient thread culture.
- [VerticaDbSetting](/class/vertica/verticadbsetting) — Vertica-specific behavior: `"` for opening/closing identifier quotes, `@` parameter prefix, no table hints, no multi-statement command text support (`IsMultiStatementExecutable = false`) except for [InsertAll](/operation/insertall) (`IsInsertAllBatchable = true`), a lower `MaxParameterCount` of `1500`, and both `RequiresDbTypeBeforeValue` and `SkipsUnreferencedParameters` set to `true` (see the [Core release notes](/release/core) for what those two control).
- [VerticaStatementBuilder](/class/vertica/verticastatementbuilder) — generates Vertica-flavored SQL for every operation: `LIMIT`/`LIMIT ... OFFSET` paging, a genuine multi-row `VALUES` list for [InsertAll](/operation/insertall), and an `UPDATE ...; INSERT ... WHERE NOT EXISTS (...)` pair (with a follow-up `SELECT LAST_INSERT_ID()` when a key is needed) for [Merge](/operation/merge)/[MergeAll](/operation/mergeall) — never a native `MERGE`, since Vertica rejects that statement outright against any table with an `IDENTITY`/`AUTO_INCREMENT` column. [Truncate](/operation/truncate) compiles to a plain `DELETE FROM t`, since Vertica has no `TRUNCATE TABLE` statement.
- [VerticaDbHelper](/class/vertica/verticadbhelper) — schema/type discovery (`GetFields`/`GetFieldsAsync`) sourced from `v_catalog.columns`/`v_catalog.primary_keys`. `GetScopeIdentity`/`GetScopeIdentityAsync` run `SELECT LAST_INSERT_ID()`.
- Resolvers — [VerticaConvertFieldResolver](/class/vertica/verticaconvertfieldresolver) (casts a field to its Vertica type when a conversion is needed, e.g. typed [ExecuteQuery](/operation/executequery) results), [VerticaDbTypeNameToClientTypeResolver](/class/vertica/verticadbtypenametoclienttyperesolver) (maps the type names returned by `v_catalog.columns` to .NET CLR types — widened to `long`/`double` since Vertica has no distinct integer/float storage widths), [DbTypeToVerticaStringNameResolver](/class/vertica/dbtypetoverticastringnameresolver) (maps `System.Data.DbType` to Vertica SQL type names, e.g. `DbType.Guid` → `UUID`), and [DbTypeNameToColumnNameResolver](/class/vertica/dbtypenametocolumnnameresolver) (maps a Vertica database type name to its base column type keyword; used internally by [RepoDb.Vertica.BulkOperations](/release/verticabulk) to generate pseudo-table column definitions).
- [TimeToDateTimePropertyHandler](/class/vertica/timetodatetimepropertyhandler) — re-bases the date component of a value read back from a `TIME` column onto `DateTime`'s default date, since Vertica's driver returns a `TIME` value combined with today's date rather than a fixed placeholder.
- Parameter attributes mirroring `VerticaParameter` members, settable per entity property: [VerticaType](/attribute/vertica/verticatype), [SourceColumn](/attribute/vertica/sourcecolumn), [SourceColumnNullMapping](/attribute/vertica/sourcecolumnnullmapping), and [SourceVersion](/attribute/vertica/sourceversion).

### Known limitations (v1)

- [Merge](/operation/merge)/[MergeAll](/operation/mergeall) compile a compound `UPDATE ...; INSERT ...` command text — both halves carrying parameters — joined by `;` into a single string submitted through `VerticaCommand.CommandText`. This has not been verified against a live Vertica instance, and appears to conflict with the very restriction `IsInsertAllBatchable`'s own remarks describe elsewhere: "a compound (\"stmt1; stmt2\") statement, which `VerticaCommand` refuses to execute once it carries a parameter." Verify this specifically before relying on [Merge](/operation/merge)/[MergeAll](/operation/mergeall) in production.
- `GetScopeIdentity`/`GetScopeIdentityAsync` run `SELECT LAST_INSERT_ID()` — unverified against a live instance.
- [MergeAll](/operation/mergeall)/[UpdateAll](/operation/updateall) issue a separate round trip per row rather than a single batched statement, since `IsMultiStatementExecutable` is `false` and neither has an `IsInsertAllBatchable`-style override. Passing an explicit `batchSize` greater than `1` to either throws a `NotSupportedException`. [InsertAll](/operation/insertall) is the exception — see `IsInsertAllBatchable` above.
- No table hints of any kind — `AreTableHintsSupported` is `false`; passing a non-null `hints` argument to any operation throws a `NotSupportedException`.
- No `TRUNCATE TABLE` statement (as of Vertica 5.0) — [Truncate](/operation/truncate) compiles to `DELETE FROM t` without a `WHERE` clause, which, unlike a real truncate, does not reset an `IDENTITY` column's next value.
- `MaxParameterCount` defaults to `1500`, lower than the `2098` most other providers default to.
- No native GUID property handler — `DbType.Guid` maps to Vertica's own `UUID` type, but there is no dedicated property handler equivalent to other providers' `GuidToByteArrayPropertyHandler` for drivers that need an explicit conversion.
- A second, near-duplicate property handler class (`RepoDb.PropertyHandlers.VerticaTimeToDateTimePropertyHandler`, as opposed to the documented `RepoDb.PropertyHandlers.Vertica.TimeToDateTimePropertyHandler`) exists in the source but is not referenced anywhere in the library or its tests — apparent leftover code from a refactor, not two intentionally-separate handlers.
- Only a standard Vertica server accessed via `Vertica.Data` is tested against.

- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `Vertica.Data` package `v24.3.0`.
