---
layout: default
sidebar: releases
title: Db2
description: "This page contains the latest information of the releases of RepoDb.Db2 library."
nav_order: 12
permalink: /release/db2
parent: RELEASES
---

# Releases for RepoDb.Db2

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.Db2) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.Db2).

## RepoDb.Db2 (v0.0.1-alpha) - Preview

Released: TBA

New
{: .label .label-green }

First (alpha) release of the Db2 provider for RepoDB, built on top of the [IBM Data Server .NET Provider (Net.IBM.Data.Db2)](https://www.nuget.org/packages/Net.IBM.Data.Db2). Targets Db2 for Linux, UNIX, and Windows (LUW) 10.5 and later, and .NET 8, .NET 9, and .NET 10.

### What's included

- [Db2Bootstrap](/class/db2/db2bootstrap) / [Db2GlobalConfiguration](/class/db2/db2globalconfiguration)`.UseDb2()` — initializes RepoDB for use with `DB2Connection` (`GlobalConfiguration.Setup().UseDb2()`), mapping the [Db2DbSetting](/class/db2/db2dbsetting), [Db2DbHelper](/class/db2/db2dbhelper), and [Db2StatementBuilder](/class/db2/db2statementbuilder) to every `DB2Connection`.
- [Db2DbSetting](/class/db2/db2dbsetting) — Db2-specific behavior: `"` for opening/closing identifier quotes, `:` parameter prefix, no table hints, no native upsert (`Merge` is hand-built instead), and multi-statement command text support (`IsMultiStatementExecutable = true`, `MultiStatementSeparator = ";"`).
- [Db2StatementBuilder](/class/db2/db2statementbuilder) — generates Db2-flavored SQL for every operation: `OFFSET ... FETCH NEXT ... ROWS ONLY` paging for [BatchQuery](/operation/batchquery)/skip-take queries, `FETCH FIRST n ROWS ONLY` for [Exists](/operation/exists) and top-N [Query](/operation/query), `MERGE INTO ... USING (SELECT ... FROM SYSIBM.SYSDUMMY1) ...` for [Merge](/operation/merge)/[MergeAll](/operation/mergeall), a genuine multi-row `VALUES (...), (...)` list for batched [InsertAll](/operation/insertall), `TRUNCATE TABLE ... IMMEDIATE`, and identity/primary-key retrieval via `SELECT ... FROM FINAL TABLE (INSERT ...)` for `Insert`/`InsertAll` plus a follow-up same-command-text `SELECT` for `Merge`/`MergeAll` (Db2 LUW's `MERGE` has no `FINAL TABLE` support).
- [Db2DbHelper](/class/db2/db2dbhelper) — schema/type discovery (`GetFields`/`GetFieldsAsync`) sourced from `SYSCAT.COLUMNS`, and generated-identity retrieval (`GetScopeIdentity`/`GetScopeIdentityAsync`) via `IDENTITY_VAL_LOCAL()` against Db2's single-row dummy table `SYSIBM.SYSDUMMY1`.
- Resolvers — [Db2ConvertFieldResolver](/class/db2/db2convertfieldresolver) (casts a field to its Db2 type when a conversion is needed, e.g. typed [ExecuteQuery](/operation/executequery) results), [Db2DbTypeNameToClientTypeResolver](/class/db2/db2dbtypenametoclienttyperesolver) (maps `SYSCAT.COLUMNS.TYPENAME` values to .NET CLR types), and [DbTypeToDb2StringNameResolver](/class/db2/dbtypetodb2stringnameresolver) (maps `System.Data.DbType` to Db2 SQL type names, e.g. `DbType.Guid` → `CHAR(16) FOR BIT DATA`).
- Property handlers — [Db2GuidToByteArrayPropertyHandler](/class/db2/db2guidtobytearraypropertyhandler) (maps a `Guid` property to/from `CHAR(16) FOR BIT DATA`, since Db2 has no native GUID type) and [Db2ByteToInt16PropertyHandler](/class/db2/db2bytetoint16propertyhandler) (maps a `byte` property to/from `SMALLINT`, since Db2 has no native single-byte integer type).
- Parameter attributes mirroring `DB2Parameter` members, settable per entity property: [Db2Type](/attribute/db2/db2type), [Db2TypeOutput](/attribute/db2/db2typeoutput), [ArrayLength](/attribute/db2/arraylength), [IsDefault](/attribute/db2/isdefault), [IsUnassigned](/attribute/db2/isunassigned), [SourceColumn](/attribute/db2/sourcecolumn), [SourceColumnNullMapping](/attribute/db2/sourcecolumnnullmapping), and [SourceVersion](/attribute/db2/sourceversion).
- [QueryMultiple](/operation/executequerymultiple)/`QueryMultipleAsync` run as a single combined, multi-statement command text (`"SELECT ... T1; SELECT ... T2"`), the same as SQL Server/MySQL/PostgreSQL — no per-type round trip.

### Known limitations (v1)

- Every statement binds parameters using `:Name`-style host variables. IBM's Data Server .NET Provider disables host-variable support by default, so the connection string **must** include `HostVarParameters=True;`, otherwise every parameterized call fails with `DB2Exception` `SQL0313N`.
- [InsertAll](/operation/insertall)/[MergeAll](/operation/mergeall)/[UpdateAll](/operation/updateall) batch multiple entities into a single round trip, but `InsertAll`'s row-to-identity correlation relies on an assumption not yet verified against a live Db2 instance: that `FINAL TABLE`'s result rows preserve the source `VALUES` list order. Verify thoroughly before relying on it in production.
- `MergeAll` throws `NotSupportedException` for a `batchSize` greater than 1 when the identity column is used as a qualifier, since a freshly-inserted row's caller-bound qualifier value can't be safely re-correlated within a mixed matched/unmatched batch. Pass an explicit non-identity qualifier, or call with `batchSize: 1`.
- `Merge`'s generated-key retrieval falls back to re-reading `MAX(<key>)` off the table rather than `IDENTITY_VAL_LOCAL()`, since Db2 LUW's per-statement autocommit clears that register before the follow-up `SELECT` runs — a best-effort read, not a guarantee, if another connection concurrently inserts into the same table.
- `Merge`/`MergeAll` against tables with several LOB columns can fail with `SQL1585N` (system temporary tablespace too small); provisioning a 32K-page temporary tablespace works around it. XML-mapped fields are not supported in `Merge`/`MergeAll` at all.
- No native GUID/`UNIQUEIDENTIFIER` type — map a `Guid` property as `byte[]`, or register [Db2GuidToByteArrayPropertyHandler](/class/db2/db2guidtobytearraypropertyhandler) per-property.
- The published package only references the Windows driver package (`Net.IBM.Data.Db2`) transitively. Linux, macOS, and other non-Windows consumers must add the matching `Net.IBM.Data.Db2-*` package themselves — see [Get Started for Db2](/tutorial/get-started-db2).
- Db2 for z/OS and Db2 for i are not currently tested against — only Db2 LUW 10.5 and later.

- Referenced the `RepoDb` package `v1.16.0`.
- Referenced the `Net.IBM.Data.Db2` / `Net.IBM.Data.Db2-lnx` package `v9.0.0.400` (`v10.0.0.200` on .NET 10).
