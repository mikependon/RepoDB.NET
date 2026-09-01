---
layout: default
sidebar: releases
title: SAP HANA
description: "This page contains the latest information of the releases of RepoDb.SapHana library."
permalink: /release/saphana
parent: RELEASES
---

# Releases for RepoDb.SapHana

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.SapHana) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.SapHana).

## RepoDb.SapHana (v0.0.1-alpha) - Preview

Released: TBA

New
{: .label .label-green }

First (alpha) release of the SAP HANA provider for RepoDB, built on top of [Sap.Data.Hana.Net.v6.0](https://www.nuget.org/packages/Sap.Data.Hana.Net.v6.0). Targets .NET 8, .NET 9, and .NET 10.

> **Verification status:** implemented and reviewed, but not yet exercised against a live SAP HANA instance. Verify the identity read-back and `UPSERT ... WITH PRIMARY KEY` semantics before relying on this package in production.

### What's included

- [SapHanaBootstrap](/class/saphana/saphanabootstrap) / [SapHanaConfiguration](/class/saphana/saphanaconfiguration)`.UseSapHana()` — initializes RepoDB for use with `HanaConnection` (`GlobalConfiguration.Setup().UseSapHana()`), mapping the [SapHanaDbSetting](/class/saphana/saphanadbsetting), [SapHanaDbHelper](/class/saphana/saphanadbhelper), and [SapHanaStatementBuilder](/class/saphana/saphanastatementbuilder) to every `HanaConnection`.
- [SapHanaDbSetting](/class/saphana/saphanadbsetting) — `"` identifier quotes, `:` parameter prefix, no table hints, no multi-statement command text support (`IsMultiStatementExecutable = false`, since SAP HANA's ADO.NET client rejects a command text containing more than one SQL statement).
- [SapHanaStatementBuilder](/class/saphana/saphanastatementbuilder) — generates SAP HANA-flavored SQL: ANSI `LIMIT`/`LIMIT ... OFFSET` paging for [Query](/operation/query)/[BatchQuery](/operation/batchquery)/skip-take queries, a native `UPSERT ... WITH PRIMARY KEY` for [Merge](/operation/merge)/[MergeAll](/operation/mergeall), and `COUNT(`/`MAX(`/`MIN(`/`SUM(` reformatted (no space before the parenthesis).
- [SapHanaDbHelper](/class/saphana/saphanadbhelper) — schema/type discovery (`GetFields`/`GetFieldsAsync`) sourced from `SYS.TABLE_COLUMNS`/`SYS.CONSTRAINTS`, scoped to `CURRENT_SCHEMA`. `GetScopeIdentity`/`GetScopeIdentityAsync` run `SELECT CURRENT_IDENTITY_VALUE() FROM DUMMY;`.
- Resolvers — [SapHanaDbTypeNameToClientTypeResolver](/class/saphana/saphanadbtypenametoclienttyperesolver) (maps `SYS.TABLE_COLUMNS.DATA_TYPE_NAME` values to .NET CLR types) and [SapHanaDbTypeToStringNameResolver](/class/saphana/saphanadbtypetostringnameresolver) (maps `Sap.Data.Hana.HanaDbType` to SAP HANA SQL type names).
- Parameter attributes mirroring `HanaParameter` members, settable per entity property: [SapHanaDbType](/attribute/saphana/saphanadbtype), [SourceColumn](/attribute/saphana/sourcecolumn), [SourceColumnNullMapping](/attribute/saphana/sourcecolumnnullmapping), [SourceVersion](/attribute/saphana/sourceversion), and [Offset](/attribute/saphana/offset).

- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `Sap.Data.Hana.Net.v6.0` package `v2.29.25`.

### Known limitations (v1)

- [InsertAll](/operation/insertall), [MergeAll](/operation/mergeall) and [UpdateAll](/operation/updateall) issue one round trip per row, since `IsMultiStatementExecutable` is `false`. Passing an explicit `batchSize` greater than `1` throws a `NotSupportedException`. Use `BulkInsert`/`BulkMerge`/`BulkUpdate` from [RepoDb.SapHana.BulkOperations](/release/saphanabulk) instead.
- No table hints of any kind — `AreTableHintsSupported` is `false`; passing a non-null `hints` argument throws a `NotSupportedException`.
- No `ConvertFieldResolver`/property-handler classes of its own — [SapHanaStatementBuilder](/class/saphana/saphanastatementbuilder) is constructed with `null` for both `convertFieldResolver` and `averageableClientTypeResolver`, falling back to [BaseStatementBuilder](/class/basestatementbuilder)'s defaults. Typed `CAST(...)` generation and average-widening behavior have not been tuned for SAP HANA's numeric-type semantics.
- No native GUID/UNIQUEIDENTIFIER type, and the core package ships no bundled Guid-mapping property handler. `SapHanaGuidToStringPropertyHandler` (maps a `Guid` property to/from an `NVARCHAR(36)` column) lives in [RepoDb.SapHana.BulkOperations](/release/saphanabulk) instead — install that package (even without using its `Bulk*` operations) if you need it, or write your own `IPropertyHandler<string, Guid>`.
- [Merge](/operation/merge) compiles to `UPSERT ... WITH PRIMARY KEY`, which matches against the primary key — its value must already be known on the entity being merged. There is no path to auto-generate and return an identity value within the same call.
- `GetScopeIdentity`/`GetScopeIdentityAsync`'s `SELECT CURRENT_IDENTITY_VALUE() FROM DUMMY;` query has not been verified against a live instance.
- Only a standard SAP HANA server (verified conceptually against HANA Express/HXE) is targeted.
